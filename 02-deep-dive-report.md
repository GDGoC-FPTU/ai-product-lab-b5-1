# Báo cáo Deep-Dive — Vin Smart Future

**Tên nhóm:** [B5.1]  
**Thành viên:**
- Đỗ Thiện Lĩnh — MSSV: 2A202600775
- Nguyễn Công Tuấn Anh — MSSV: 2A202600977

**Bài toán được chọn:** Triage ticket bảo hành xe điện thủ công (Quick Problem Card #1 — VinFast)

---

## Quyết định lựa chọn của nhóm

Nhóm chọn **Card #1 — Triage ticket bảo hành xe điện** vì tác động vận hành trực tiếp (SLA CSKH, tỷ lệ escalation), metric đo được rõ, và phù hợp kiến trúc LLM + Agent có HITL.

| Card đã xem xét | Lý do không chọn làm Deep-Dive |
|-----------------|--------------------------------|
| #2 Đối chiếu hóa đơn sạc | Phù hợp Rule-based hơn; cần tích hợp OCPP/payment trước khi thêm LLM |
| #3 Tra cứu phụ tùng KTV | Giá trị cao nhưng phụ thuộc master data SAP/DMS; scope POC rộng hơn 1 sprint |

---

## Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow Mapping

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận ticket  │     │ Đọc mô tả    │     │ Tra VIN +    │     │ Phân loại &  │
│ từ khách     │ ──→ │ lỗi / triệu  │ ──→ │ lịch sử xe   │ ──→ │ định tuyến   │
│ (đa kênh)    │     │ chứng        │     │ (nhiều hệ    │     │ ticket       │
│              │     │              │     │ thống)       │     │              │
│ Ai: CSKH     │     │ Ai: CSKH     │     │ Ai: CSKH     │     │ Ai: CSKH/VSSC│
│ ⏱ ~2 phút    │     │ ⏱ ~3 phút    │     │ ⏱ 8-10 phút 🔴│     │ ⏱ 5-7 phút 🔴│
│ In: Call/App │     │ In: Mô tả    │     │ In: VIN      │     │ In: Hồ sơ    │
│ Out: Ticket  │     │ Out: Ghi chú │     │ Out: Lịch sử │     │ Out: Route   │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
       │                    │                    │                    │
       └────────────────────┴────────────────────┴────────────────────┘
                                    🔄 Handoff
                                    (CRM → DMS → VSSC queue)
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Gán xưởng /  │
                                                               │ lịch hẹn     │
                                                               │ Ai: VSSC     │
                                                               │ ⏱ ~2 phút    │
                                                               └──────────────┘

🔴 Bottleneck: Bước 3–4 (tra cứu đa hệ thống + phân loại thủ công)
🔄 Handoff: CSKH ↔ DMS ↔ hàng đợi VSSC; chuyển giao thông tin bằng copy-paste
⏱ Tổng thời gian xử lý thủ công: ~20–24 phút/ticket (ước tính từ khảo sát nội bộ)
```

---

### 3.2. Problem Statement (6-field)

| Field | Nội dung |
|-------|----------|
| **1. Actor / Operator** | Nhân viên CSKH tổng đài và điều phối viên VSSC (VinFast Service Center) xử lý ticket bảo hành xe điện qua CRM, DMS và hệ thống lịch sử xe. |
| **2. Current Workflow** | Khách báo lỗi qua app/hotline → CSKH tạo ticket, đọc mô tả triệu chứng → tra VIN và lịch sử sửa chữa trên 2–3 hệ thống → phân loại mức độ (P1–P3) và định tuyến tới xưởng/đội kỹ thuật → gán lịch hẹn. Toàn bộ bước 3–4 thủ công, ~15–17 phút/ticket. |
| **3. Bottleneck** | Bước 3–4: đọc mô tả tự do tiếng Việt, đối chiếu mã lỗi OBD (nếu có), tra lịch sử và chọn queue đúng — dễ sai loại ticket, gây escalation L2 (~40%). |
| **4. Business Impact** | ~2.000 ticket bảo hành/tháng (ước tính 1 hub). 15 phút/ticket × 2.000 ≈ 500 giờ CSKH/tháng cho triage; escalation L2 làm kéo SLA phản hồi, ảnh hưởng NPS và chi phí nhân sự L2. |
| **5. Success Metric** | 1. Giảm thời gian triage từ 15–20 phút → **dưới 3 phút/ticket** (p90).<br>2. Giảm tỷ lệ escalation L2 từ ~40% → **dưới 15%**.<br>3. **≥85%** gợi ý phân loại/định tuyến được CSKH chấp nhận không chỉnh sửa (acceptance rate). |
| **6. Operational Boundary** | AI được phép: đọc mô tả ticket + VIN, gợi ý mã phân loại, queue đích và tóm tắt cho CSKH (**draft only**). **Cấm:** tự đóng ticket, tự cam kết thời gian sửa chữa, tự đổi chính sách bảo hành, hoặc gửi thông báo trực tiếp cho khách không qua CSKH duyệt. Mọi quyết định định tuyến cuối cùng cần **Human-in-the-loop**. |

---

### 3.3. Future-State Flow & AI Fit

**AI-Fit Matrix (so sánh nhanh):**

| Hướng | Phù hợp? | Ghi chú |
|-------|----------|---------|
| Rule / State-machine | Một phần | Routing theo mã lỗi OBD chuẩn hóa, SLA theo P-level |
| **LLM Feature** | **Chính** | Đọc mô tả tự do, tóm tắt, gợi ý phân loại + queue |
| Agentic Loop | Hạn chế | Chỉ sau khi có API ổn định và guardrail; POC dùng LLM + HITL |

**Future-State Flow:**

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ 🔵 Bước 3    │     │ 🟢 Bước 4    │
│ Nhận ticket  │     │ CSKH xác     │     │ AI đọc mô tả │     │ CSKH review  │
│ (như cũ)     │ ──→ │ nhận VIN     │ ──→ │ + tra API    │ ──→ │ & duyệt gợi │
│              │     │ cơ bản       │     │ → draft phân │     │ ý định tuyến │
│ Ai: CSKH     │     │ Ai: CSKH     │     │ loại/queue   │     │ Ai: CSKH     │
│ ⏱ ~2 phút    │     │ ⏱ ~1 phút    │     │ ⏱ ~30–60s    │     │ ⏱ ~1 phút    │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Gán xưởng    │
                                                               │ (rule + API) │
                                                               │ ⏱ ~1 phút    │
                                                               └──────────────┘

↩️ Fallback: Nếu AI confidence thấp hoặc thiếu dữ liệu VIN → chuyển queue "manual triage"
   và CSKH làm quy trình cũ; log case để huấn luyện lại.
```

- **🔵 AI Step:** NLP đọc mô tả lỗi, gọi API lịch sử xe, xuất JSON gợi ý `{category, priority, suggested_queue, summary}` kèm tag `[DRAFT_ONLY]`.
- **🟢 HITL:** CSKH bắt buộc bấm Approve/Edit trước khi ticket vào VSSC.
- **↩️ Fallback:** Timeout API, confidence &lt; ngưỡng, hoặc ticket P1 (an toàn) → không auto-route, escalate supervisor.

---

## Phase 5 — EVALUATE

### AI Readiness Checklist

| # | Tiêu chí | Trạng thái | Ghi chú |
|---|----------|------------|---------|
| 1 | Có dữ liệu mẫu/logs sạch để test? | ☑ Một phần | Cần 500–1.000 ticket đã gán nhãn (category + queue) từ 3 tháng gần nhất |
| 2 | Rủi ro AI sai nằm trong tầm kiểm soát? | ☑ Có | HITL + Fallback manual; không auto gửi khách hàng |
| 3 | Stakeholder sẵn sàng đổi quy trình? | ☑ Có | CSKH/VSSC đồng ý pilot 1 hub trước khi rollout |

### Quyết định cuối cùng

**[x] GO (Bắt đầu xây dựng Prototype)** — với scope hẹp: 1 hub, chỉ gợi ý draft phân loại/định tuyến, không thay thế CSKH.

**Justification (kỹ thuật & chi phí):**

- **Kỹ thuật:** Bài toán có input cấu trúc (VIN) + text tự do — phù hợp LLM Feature; Rule xử lý SLA/P-level; không cần Agent full autonomy ở POC.
- **Chi phí ước lượng (3 tháng POC):** ~40–60M VND (API Gemini/Vertex, 0.5 FTE ML engineer, tích hợp read-only CRM/DMS). Tiết kiệm ước tính ~300 giờ CSKH/tháng nếu đạt metric → ROI dương sau tháng 4–5.
- **Rủi ro:** Hallucination mã lỗi — giảm bằng RAG trên knowledge base nội bộ + bắt buộc HITL.
- **Không chọn NOT YET** vì đã có ticket history đủ pilot; **không NO-GO** vì Rule-only không xử lý tốt mô tả lỗi tự do tiếng Việt.
