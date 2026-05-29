# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | VinFast | Lặp lại | Đối chiếu hóa đơn sạc điện thủ công giữa app, trụ sạc và hóa đơn VAT |
| 2 | VinFast | Tốn thời gian | Phân loại & định tuyến ticket bảo hành thủ công qua nhiều hệ thống |
| 3 | VinFast | AI có thể tốt hơn | Chatbot không hiểu câu hỏi kỹ thuật, escalation rate >60% |
| 4 | VinFast | Pain từ người khác | Kỹ thuật viên mất 25–40 phút tra phụ tùng trên 3–4 hệ thống rời rạc |
| 5 | VinFast | Lặp lại + Tốn thời gian | Kiểm tra chất lượng cuối chuyền nhập tay 200 hạng mục/xe |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Triage ticket bảo hành xe điện thủ công  │
│ Công ty thành viên: VinFast                                 │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH tổng đài & VSSC        │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhận yêu cầu ──> 2. Đọc mô tả lỗi ──>                 │
│   3. Tra VIN + lịch sử ──> 4. Phân loại ──> 5. Định tuyến  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3–4 (⏱ 15–20 phút)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 3–4: NLP đọc mô tả → tra VIN → gợi ý phân loại      │
│   & định tuyến tự động                                      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm triage từ 15–20 phút ──> dưới 3 phút/ticket         │
│   Giảm escalation L2 từ 40% ──> dưới 15%                   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [x] Agent│
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Đối chiếu hóa đơn sạc điện thủ công      │
│ Công ty thành viên: VinFast                                 │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên kế toán vận hành trạm sạc   │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Export log OCPP ──> 2. Đối chiếu app payment ──>       │
│   3. Kiểm tra VAT ──> 4. Flag & xử lý sai lệch             │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ 3–4 phút/GD)   │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 2: Auto-reconcile realtime OCPP + app + VAT,         │
│   chỉ flag exception cho human review                       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm workload từ ~800 giờ/tháng ──> dưới 80 giờ/tháng   │
│   Tỷ lệ sai lệch từ 3–5% ──> dưới 0.5%                     │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent│
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): KTV tra cứu phụ tùng trên nhiều hệ thống │
│ Công ty thành viên: VinFast                                 │
│                                                             │
│ Ai đang đau (Actor)? Kỹ thuật viên xưởng tại VSSC          │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Nhận xe + đọc OBD ──> 2. Tra phụ tùng (SAP/DMS/PDF)  │
│   ──> 3. Kiểm tra tồn kho ──> 4. Đặt hàng ──> 5. Sửa chữa │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 (⏱ 25–40 phút/xe) │
│ AI có thể nhảy vào hỗ trợ ở bước nào?                      │
│   Bước 2: Nhập VIN/mã OBD → AI gợi ý mã phụ tùng           │
│   + kiểm tra tồn kho realtime trong 1 interface             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                       │
│   Giảm look-up từ 25–40 phút ──> dưới 5 phút/xe            │
│   Tăng throughput xưởng thêm 20–25%                         │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent│
└─────────────────────────────────────────────────────────────┘
```
