# Thông tin nhóm
Tên nhóm: TM
Nguyễn Tuấn Minh - 2A202600692

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow
Quy trình hiện tại

```text
+-------------------+     +-------------------+     +-------------------+     +-------------------+
| Bước 1            |     | Bước 2            |     | Bước 3            |     | Bước 4            |
| Tài xế gọi        |     | Ghi nhận thủ công |     | Tìm xe sạc        |     | Gọi điện điều     |
| tổng đài          |     |                   |     | gần nhất thủ công |     | phối xe sạc       |
|                   | --> |                   | --> |                   | --> |                   |
| Ai: Tài xế        |     | Ai: Dispatcher    |     | Ai: Dispatcher    |     | Ai: Dispatcher    |
| In: Cuộc gọi      |     | ⏱️ ~3 phút    🔴  |     | ⏱️ ~4 phút 🔄    |     | ⏱️ ~3 phút 🔄    |
| Out: Yêu cầu sạc  |     | In: Thông tin     |     | In: Vị trí tài xế |     | In: Sđt điều phối |
|                   |     | Out: Log sự cố    |     | Out: Xe sạc tối ưu|     | Out: Xác nhận     |
+-------------------+     +-------------------+     +-------------------+     +-------------------+
                                                                                        |
                                                                                        v
                                                                              +-------------------+
                                                                              | Bước 5            |
                                                                              | Nhắn tin xác nhận |
                                                                              | tài xế            |
                                                                              |                   |
                                                                              | Ai: Dispatcher    |
                                                                              | ⏱️ ~2 phút        |
                                                                              | In: Thông tin xe  |
                                                                              | Out: SMS/Zalo     |
                                                                              +-------------------+

🔴 = Bước bottleneck chính (Bước 2: Dễ bỏ sót khi dispatcher bận nhiều ca)
🔄 = Điểm chuyển giao thông tin
⏱️ Tổng thời gian xử lý thủ công: ~12 phút/lượt.

```
---

## 3.2. Problem Statement (6-field) & Metrics (15 min)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Dispatcher trực ca Xanh SM + Tài xế EV gặp sự cố pin |
| **2. Current Workflow** | Tài xế gọi tổng đài → Dispatcher ghi nhận tay → Tra bản đồ tìm xe sạc → Gọi điện điều xe → Nhắn tin xác nhận. Công cụ: điện thoại, bản đồ giấy/Google Maps, chat nội bộ. |
| **3. Bottleneck** | Bước ghi nhận + tìm xe sạc thủ công (~7 phút): dispatcher phải tự phán đoán mức pin, tra tọa độ, không có hệ thống hỗ trợ quyết định. Dễ nhầm khi cùng lúc xử lý nhiều ca. |
| **4. Business Impact** | ~30–50 lượt/ngày × 12 phút = 6–10 giờ công dispatcher/ngày. Rủi ro xe chết máy giữa đường gây tai nạn, phạt SLA vận hành, mất uy tín thương hiệu. |
| **5. Success Metric** | Thời gian xử lý từ báo cáo → xe sạc được điều: dưới 2 phút/lượt (hiện ~12 phút). Tỷ lệ xe chết máy giữa đường do pin: 0 trường hợp/tháng. |
| **6. Operational Boundary** | AI được phép: Soạn bản nháp lệnh điều phối, kiểm tra mức pin, gợi ý xe sạc gần nhất trong 5km. AI không được: Tự gửi lệnh điều xe mà không có dispatcher duyệt. Gợi ý trạm sạc xa >5km khi pin <5%. Cần duyệt: Mọi tin nhắn gửi tài xế phải có tiền tố [DRAFT_ONLY] và chờ dispatcher phê duyệt.|

---

## 3.3. Future-State Flow & AI Fit

* **AI Fit:** [ ] Rule / State-Machine  [X] LLM Feature  [ ] Agentic Loop

```text
+-------------------+     +-------------------+     +-------------------+     +-------------------+
| Bước 1            |     | Bước 2 (Bước AI)  |     | Bước 3 (Bước AI)  |     | Bước 4 (Bước Người)|
| Tài xế báo cáo    |     | LLM phân tích     |     | LLM soạn          |     | Dispatcher        |
| qua app           |     | pin + tọa độ      |     | lệnh điều xe      |     | xem xét duyệt     |
|                   | --> |                   | --> | [DRAFT_ONLY]      | --> |                   |
| Ai: Tài xế        |     | Ai: LLM           |     | Ai: LLM           |     | Ai: Dispatcher    |
| In: Data từ App   |     | ⚡ <5 giây 🔵    |     | ⏱️ Tự động 🔵    |     | ⏱️ ~30 giây 🟢    |
| Out: Yêu cầu cứu hộ|     | In: Pin + Tọa độ |     | In: Data phân tích|     | In: Bản thảo lệnh |
|                   |     | Out: KQ phân tích |     | Out: Lệnh nháp    |     | Out: Lệnh duyệt   |
+-------------------+     +-------------------+     +-------------------+     +-------------------+
                                                                                        |
                                                                                        v
+-------------------+                                                         +-------------------+
| Bước 6            |                                                         | Bước 5            |
| Xong              |                                                         | Điều xe sạc       |
|                   | <------------------------------------------------------- | + thông báo tự động|
| ⏱️ <2 phút         |                                                         |                   |
|                   |                                                         | Ai: Hệ thống      |
| Trạng thái: Hoàn tất|                                                       | Out: Lệnh đi xe   |
+-------------------+                                                         +-------------------+

===================================== VÙNG QUẢN LÝ VÀ LƯU Ý =====================================
* 🔵: Bước 2 -> Bước 3
* 🟢: Bước 4 (Dispatcher giám sát và duyệt)

⚠️ Quy tắc xử lý ngoại lệ (Exception Rules):
- Nếu LLM không chắc chắn -> Chuyển toàn bộ cho dispatcher xử lý thủ công.
- Tình huống: Pin <5% + không có xe sạc trong 5km -> Cảnh báo khẩn cấp ưu tiên cao.

```
---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?
3. [x] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[x] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> Quyết định GO cho **Card #1 — Xanh SM Điều phối xe sạc di động khi tài xế cạn pin** dựa trên ba bằng chứng cụ thể. Thứ nhất, dữ liệu vận hành sẵn có: Xanh SM đã có logs GPS, lịch sử sự cố pin và dữ liệu trạm sạc VinFast theo thời gian thực — đủ để test prototype ngay mà không cần thu thập thêm. Thứ hai, rủi ro được kiểm soát: AI chỉ soạn bản nháp [DRAFT_ONLY], mọi lệnh điều phối đều qua dispatcher duyệt trước khi gửi, và có fallback rõ ràng khi AI không chắc chắn. Thứ ba, ROI thuyết phục: ~80 sự cố/ngày × giảm 12 phút/lượt = tiết kiệm ~16 giờ công dispatcher/ngày, tác động trực tiếp đến doanh thu tài xế và trải nghiệm vận hành. Scope prototype hẹp (chỉ xử lý luồng pin < 5% và gợi ý trạm sạc trong 5km), đủ để validate giả thuyết trong 2–3 tuần mà không cần đầu tư lớn.

---
