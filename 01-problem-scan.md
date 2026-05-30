## 🏛️ Bối cảnh: Tôi là ai?

Tôi là **Minh**, AI Engineer tại **Vin Smart Future**. Trong buổi này, tôi được giao nhiệm vụ phối hợp với Khối Vận Hành của Xanh SM (GSM) để tìm kiếm các cơ hội tối ưu hóa vận hành bằng trí tuệ nhân tạo. 

Thông qua việc phân tích các quy trình vận hành hiện tại của Xanh SM, tôi nhận thấy nhiều tác vụ vẫn đang được xử lý thủ công như kiểm tra ngoại quan xe, điều phối xe giờ cao điểm và đánh giá chất lượng tổng đài. Những bottleneck này gây mất thời gian, tăng chi phí vận hành và làm giảm trải nghiệm của tài xế cũng như khách hàng.

Bài toán tôi lựa chọn trong buổi Lab hôm nay tập trung vào việc ứng dụng AI để giảm tải cho đội vận hành và tăng hiệu quả xử lý theo thời gian thực.

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội (Cá nhân)

Dùng **4 Lenses** quét qua vận hành của các công ty thành viên Vingroup.

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Xanh SM | Stakeholder Pain | Tài xế cạn pin phải tự gọi tổng đài xin hỗ trợ; dispatcher phải điều xe sạc di động thủ công, chậm và dễ sai sót. |
| 2 | VinFast | Repetitive | Đội CSKH phải trả lời thủ công hàng trăm câu hỏi lặp lại về chính sách bảo hành pin VinFast mỗi ngày. |
| 3 | Vinhomes | AI-upgrade | Chatbot hỗ trợ cư dân hiện chỉ trả lời theo kịch bản cứng, không xử lý được yêu cầu phức tạp như đặt lịch sửa chữa hay tra cứu hóa đơn. |
| 4 | Vinmec | Time-consuming | Nhân viên hành chính nhập tay dữ liệu từ phiếu khám giấy vào hệ thống HIS, dễ sai và tốn thời gian. |
| 5 | Vinpearl | AI-upgrade | Hệ thống gợi ý tour và hoạt động tại VinWonders hiện còn dựa vào staff tư vấn thủ công, chưa cá nhân hóa theo profile khách.|

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards (Cá nhân)

Chọn top 3 từ danh sách SCAN: **#1 (Xanh SM Cạn pin), #3 (Vinhomes Chatbot), #4 (Vinmec Nhập liệu).**

## Thẻ bài toán tiêu biểu: Card #1 — Xanh SM — Điều phối xe sạc di động khi tài xế cạn pin

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Tài xế cạn pin không có kênh tự báo cáo;  |
|  dispatcher điều xe sạc di động hoàn toàn thủ công, chậm và | 
|  dễ bỏ sót.                                                 │
│                                                             
│ Công ty thành viên: [ ] VinFast  [X] Xanh SM  [ ] Vinhomes │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________ │
│                                                             │
│ Ai đang đau (Actor)? Tài xế Xanh SM + Dispatcher trực ca    │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│ 1. Tài xế gọi tổng đài ──> 2. Dispatcher ghi nhận thủ công  |
| ──> 3. Tìm xe sạc gần nhất ──> 4. 4. Gọi điện điều phối ──> |
| 5. Nhắn tin xác nhận tài xế                                 |
|                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–4 (ghi nhận + tìm + |
| điều phối thủ công)│(⏱ 8-10 phút/lượt)                     │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–5: Mô hình ngôn|
| ngữ nhận thông tin từ tài xế, kiểm tra mức pin, tự soạn lệnh|
| điều phối + bản nháp tin nhắn chờ dispatcher duyệt.         |
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian xử lý  |
|  từ ~10 phút ──> dưới 2 phút/lượt; 0 trường hợp xe chết máy  |
|  giữa đường do pin cạn.                                      │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [X] LLM [X]Agent │
└─────────────────────────────────────────────────────────────┘
```

## Thẻ bài toán tiêu biểu: Card #3 — Vinhomes — Chatbot hỗ trợ cư dân thông minh
```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Chatbot hiện chỉ xử lý kịch bản cứng,     |
| không giải quyết được yêu cầu phức tạp như đặt lịch sửa chữa| 
| hay tra cứu hóa đơn.                                        │
│                                                             |
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [X] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Cư dân Vinhomes + nhân viên chăm sóc   |
| khách hàng tòa nhà                                          │                            
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│ 1. Cư dân nhắn chatbot ──> 2. Bot không hiểu, chuyển người  |
| ──> 3. Nhân viên đọc yêu cầu ──> 4. Tra hệ thống thủ côn ──>|
| 5. Phản hồi cư dân                                          |
|                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2–4 Bước 2–4 (chuyển  |
| tiếp + tra thủ công)│(⏱ 15-20 phút/lượt)                    |   
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–5: Tác nhân AI |
| hiểu ngôn ngữ tự nhiên, kết nối API hệ thống tòa nhà để tự  |
| tra hóa đơn, đặt lịch và trả lời trực tiếp.                 |
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Tỷ lệ tự giải quyết   |
| không cần chuyển tiếp tăng từ ~30% ──> trên 75%; thời gian  |
| phản hồi từ 15 phút ──> dưới 1 phút.                        │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [X] LLM [X]Agent   │
└─────────────────────────────────────────────────────────────┘
```
## Thẻ bài toán tiêu biểu: Card #4 — Vinmec — Tự động hóa nhập liệu phiếu khám vào hệ thống HIS
```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Nhân viên hành chính nhập tay dữ liệu từ  |
| phiếu khám giấy vào hệ thống HIS, gây sai sót và tốn nhiều  |
| giờ công mỗi ngày.                                          │
│                                                             |
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes │
│                     [X] Vinmec   [ ] Khác (Ghi rõ)________ │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên hành chính y tế + bác sĩ nhận|
| hồ sơ sai/thiếu                                             │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│ 1. Bệnh nhân điền phiếu giấy ──> 2. Thu phiếu tại quầy      |
| ──> 3. Nhân viên nhập tay vào HIS ──> 4. Kiểm tra lại dữ    |
| liệu ──> 5. Chuyển hồ sơ cho bác sĩ                         |
|                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (nhập tay)          |
| (⏱ 5-8 phút/phiếu)                                         │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3–4: Nhận dạng ký|
| tự quang học + mô hình ngôn ngữ trích xuất và chuẩn hóa dữ  |
| liệu từ ảnh phiếu, tự điền vào HIS, đánh dấu trường bất     |
| thường để người kiểm tra.                                   |
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Giảm thời gian nhập từ|
| ~6 phút ──> dưới 30 giây/phiếu; tỷ lệ lỗi từ ~4% ──>        |
| dưới 0.5%.                                                  │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [X] LLM [ ]Agent   │
└─────────────────────────────────────────────────────────────┘
```

---
# 🗳️ Quyết định lựa chọn của nhóm:
Nhóm quyết định chọn bài toán **"Card #1 — Xanh SM Điều phối xe sạc di động khi tài xế cạn pin"** để thực hiện Deep-Dive.

## Lý do lựa chọn và loại bỏ các thẻ khác:
* **Card #3 (Vinhomes Chatbot):** Mặc dù pain point rõ ràng và dễ đo lường, bài toán yêu cầu tích hợp nhiều API hệ thống quản lý tòa nhà khác nhau. Scope quá rộng để prototype trong thời gian lab, khó thiết lập ranh giới vận hành chặt chẽ.
* **Card #4 (Vinmec Nhập liệu):** ROI thuyết phục nhưng phụ thuộc nặng vào chất lượng OCR với chữ viết tay tiếng Việt, đồng thời dữ liệu y tế nhạy cảm đặt ra nhiều ràng buộc pháp lý khó xử lý trong prototype ban đầu.
---