### 📝 List bài toán của tôi:
| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | **VinCom Retail** | Lặp lại | Phân tích và tính toán lại các sản phẩm và nhu cầu mua sắm của người tiêu dùng |
| 2 | **VinCom Retail** | Tốn thời gian | Điều phối nhân viên xắp xếp hàng hóa, sử lí các tình huống về sản phẩm tại các gian hàng. |
| 3 | **VinCom Retail** | Lặp lại | So khớp hóa đơn với việc áp dụng các hình thức thanh toán và các hình thức giảm giá. |
| 4 | **VinCom Retail** | AI-upgrade | Hệ thống chat bot phản hồi, thông báo, phân tích nhu cầu hành vi mua hàng, chương trình giảm giá qua tài khoản cá nhân. |
| 5 | **VinCom Retail** | Pain từ người khác | Khách hàng phàn nàn khó khăn về việc tìm các gian hàng cần thiết. |
### 📝 TOP 3 bài toán:
```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                     
│                                                             
│ Bài toán (1 câu):   │ Phân tích và tính toán lại các sản phẩm và nhu cầu mua sắm của người tiêu dùng
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  
│                     [ ] Vinmec   [x] Khác (Ghi rõ) Vincom  
│                                                             
│ Ai đang đau (Actor)? Quản lí 
│                                                             
│ Workflow thủ công hiện tại (3-5 bước):                      
│   1. Check lại hóa đơn mua hàng ──> 2. check thủ công các mặt hàng bán chạy ──> 3. Lập bản báo cáo doanh thu các mặt hàng theo khoảng time ──> 4. Tổng kết, phân tích lại nhu cầu mua hàng.                   
│                                                             
│ Bước nào tốn thời gian/lỗi nhất? _3,4__ (⏱ _120__ phút/lượt)      
│ AI có thể nhảy vào hỗ trợ ở bước nào? _______3,4______________ 
│                                                             
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian soạn phản hồi từ 120 min ──> under 5 min
│
│                                                             
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [1] Agent 
└─────────────────────────────────────────────────────────────┘



┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Cá nhân hóa trải nghiệm mua sắm thông qua │
│                   hệ thống chatbot tương tác và thông báo   │
│                   chương trình ưu đãi tự động cho khách hàng.│
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [x] Khác: Vincom Retail    │
│                                                             │
│ Ai đang đau (Actor)? Đội ngũ Marketing / Chăm sóc khách hàng│
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Thu thập và lọc data khách hàng (lịch sử mua sắm) ──>  │
│   2. Phân nhóm khách hàng thủ công theo hành vi ──>         │
│   3. Thiết kế nội dung ưu đãi cho từng nhóm ──>             │
│   4. Gửi tin nhắn/thông báo hàng loạt qua SMS/App.          │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? _2,3__ (⏱ _240__ phút/đợt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? _______2,3,4____________│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Tăng tỷ lệ mở/tương   │
│ tác thông báo từ 5% lên >25%; giảm 90% thời gian phân loại. │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [ ] LLM  [x] Agent │
└─────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Hỗ trợ khách hàng tìm kiếm vị trí và chỉ  │
│                   đường đến các gian hàng trong trung tâm   │
│                   thương mại một cách nhanh chóng.          │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [x] Khác: Vincom Retail    │
│                                                             │
│ Ai đang đau (Actor)? Khách hàng đến mua sắm / Quầy lễ tân   │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1. Khách đi tìm sơ đồ giấy hoặc quầy thông tin ──>        │
│   2. Đợi xếp hàng gặp nhân viên lễ tân (nếu quầy đông) ──>  │
│   3. Hỏi vị trí gian hàng ──> 4. Nghe nhân viên hướng dẫn   │
│   hoặc nhìn sơ đồ để tự ghi nhớ lộ trình di chuyển.         │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? _1,4__ (⏱ _15___ phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? _______1,3,4____________│
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian tìm    │
│ kiếm gian hàng của khách từ 15 min ──> dưới 1 min qua App.  │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```