## 🏛️ Bối cảnh: Tôi là ai?

Tôi là **Minh**, AI Engineer tại **Vin Smart Future**. Trong buổi này, tôi được giao nhiệm vụ phối hợp với Khối Vận Hành của Xanh SM (GSM) để tìm kiếm các cơ hội tối ưu hóa vận hành bằng trí tuệ nhân tạo. 

Thông qua việc phân tích các quy trình vận hành hiện tại của Xanh SM, tôi nhận thấy nhiều tác vụ vẫn đang được xử lý thủ công như kiểm tra ngoại quan xe, điều phối xe giờ cao điểm và đánh giá chất lượng tổng đài. Những bottleneck này gây mất thời gian, tăng chi phí vận hành và làm giảm trải nghiệm của tài xế cũng như khách hàng.

Bài toán tôi lựa chọn trong buổi Lab hôm nay tập trung vào việc ứng dụng AI để giảm tải cho đội vận hành và tăng hiệu quả xử lý theo thời gian thực.

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dùng **4 Lenses** quét qua vận hành của các công ty thành viên Vingroup.

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Xanh SM | Stakeholder Pain | Hệ thống phát hiện gian lận cuốc xe và xử lý khiếu nại tài xế còn chậm, phải kiểm tra GPS thủ công. |
| 2 | Xanh SM | Time-consuming | Nhân viên Hub phải kiểm tra ảnh tình trạng xe đầu/cuối ca bằng mắt thường gây mất nhiều thời gian. |
| 3 | Xanh SM | AI-upgrade | Hệ thống điều phối xe chưa dự đoán tốt nhu cầu khách vào giờ cao điểm và thời tiết xấu. |
| 4 | Xanh SM | Repetitive | Đội QA phải nghe thủ công các cuộc gọi tổng đài để đánh giá chất lượng dịch vụ. |
| 5 | VinFast | AI-upgrade | Lịch bảo dưỡng pin và xe điện hiện còn dựa nhiều vào chu kỳ cố định thay vì dự đoán bằng AI.|

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn top 3 từ danh sách SCAN: **#2 (Fleet Inspection), #3 (Predictive Dispatching), #4 (QA Call Center Automation).**

## Thẻ bài toán tiêu biểu: Card #2 — Xanh SM Kiểm tra ngoại quan xe

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                      │
│                                                             │
│ Bài toán (1 câu): Nhân viên Hub của Xanh SM phải kiểm tra  │
│ ảnh tình trạng xe đầu/cuối ca bằng mắt thường gây mất      │
│ nhiều thời gian và dễ bỏ sót hư hỏng.                      │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [X] Xanh SM  [ ] Vinhomes │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________ │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên Hub và tài xế Xanh SM.      │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Tài xế chụp ảnh xe ──> 2. Upload lên hệ thống ──>     │
│   3. Nhân viên kiểm tra thủ công ──> 4. Xác nhận bàn giao  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Kiểm tra hình ảnh thủ     │
│ công (⏱ 10-15 phút/xe)                                     │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? AI Computer Vision   │
│ có thể tự động phát hiện vết xước, móp méo hoặc thiếu phụ  │
│ kiện trên xe.                                               │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian kiểm  │
│ tra xe từ 15 phút xuống dưới 3 phút và giảm tranh chấp     │
│ bàn giao xe xuống dưới 20%.                                │
│                                                             │
│ Quick Architecture: [ ] No AI  [X] Rule  [ ] LLM [X]Agent │
└─────────────────────────────────────────────────────────────┘
```

## Thẻ bài toán tiêu biểu: Card #3 — Xanh SM Predictive Dispatching
```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                      │
│                                                             │
│ Bài toán (1 câu): Hệ thống điều phối xe của Xanh SM chưa   │
│ dự đoán tốt nhu cầu khách vào giờ cao điểm và thời tiết    │
│ xấu dẫn đến nhiều cuốc xe bị hủy.                           │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [X] Xanh SM  [ ] Vinhomes │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________ │
│                                                             │
│ Ai đang đau (Actor)? Tài xế Xanh SM, khách hàng và đội     │
│ điều phối vận hành.                                         │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Hệ thống nhận yêu cầu đặt xe ──> 2. Điều phối theo    │
│   GPS hiện tại ──> 3. Tài xế tự tìm khu vực có khách ──>   │
│   4. Khách chờ lâu hoặc hủy chuyến                         │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Điều phối xe theo thời    │
│ gian thực chưa chính xác (⏱ 5-10 phút/chuyến)              │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? AI có thể dự đoán    │
│ nhu cầu khách theo thời tiết, giờ cao điểm và dữ liệu lịch │
│ sử để gợi ý vị trí đón khách trước.                        │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm tỷ lệ hủy       │
│ chuyến từ 15% xuống dưới 5% và giảm quãng đường chạy rỗng  │
│ ít nhất 10%.                                                │
│                                                             │
│ Quick Architecture: [ ] No AI  [X] Rule  [X] LLM [X]Agent │
└─────────────────────────────────────────────────────────────┘
```
## Thẻ bài toán tiêu biểu: Card #4 — Xanh SM QA Call Center Automation
```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #4                                      │
│                                                             │
│ Bài toán (1 câu): Đội QA của Xanh SM phải nghe thủ công    │
│ các cuộc gọi tổng đài để đánh giá chất lượng dịch vụ gây   │
│ mất nhiều thời gian và bỏ sót phản hồi tiêu cực.           │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [X] Xanh SM  [ ] Vinhomes │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________ │
│                                                             │
│ Ai đang đau (Actor)? Đội QA Call Center và khách hàng.     │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Khách hàng gọi tổng đài ──> 2. QA chọn ngẫu nhiên     │
│   cuộc gọi ──> 3. Nghe và đánh giá thủ công ──> 4. Tổng    │
│   hợp báo cáo cuối ngày                                    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Nghe và đánh giá thủ công │
│ cuộc gọi (⏱ 15-20 phút/cuộc gọi)                           │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? AI Speech-to-Text và │
│ LLM có thể tự động phân tích nội dung, cảm xúc khách hàng  │
│ và phát hiện lỗi hệ thống theo thời gian thực.             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Tự động phân tích    │
│ trên 95% cuộc gọi và giảm thời gian phát hiện sự cố từ     │
│ 12 giờ xuống dưới 15 phút.                                 │
│                                                             │
│ Quick Architecture: [ ] No AI  [X] Rule  [X] LLM [X]Agent │
└─────────────────────────────────────────────────────────────┘
```

---
# 🗳️ Quyết định lựa chọn của nhóm:
Nhóm quyết định chọn bài toán **"Card #3 — Xanh SM Điều phối xe dự đoán nhu cầu khách hàng"** để thực hiện Deep-Dive.

## Lý do lựa chọn và loại bỏ các thẻ khác:
* **Card #2 (Kiểm tra ngoại quan xe):** Mặc dù có thể ứng dụng Computer Vision để phát hiện hư hỏng xe, nhưng bài toán yêu cầu dữ liệu ảnh lớn, nhiều góc chụp chuẩn hóa và cần huấn luyện mô hình chuyên biệt. Ngoài ra, độ chính xác thấp có thể gây tranh chấp trách nhiệm giữa tài xế và công ty.
* **Card #4 (QA Call Center Automation):** Đây là bài toán có giá trị phân tích dài hạn nhưng chủ yếu phục vụ back-office và giám sát chất lượng nội bộ. Tác động đến vận hành thời gian thực chưa rõ rệt bằng bài toán điều phối xe trong giờ cao điểm.

---