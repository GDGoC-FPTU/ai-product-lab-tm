# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow
Quy trình xử lý sự cố hết pin thực địa hiện tại của điều phối viên Xanh SM:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Khách đặt xe │ --> │ Hệ thống tìm │ --> │ Tài xế tự di │ --> │ Khách chờ lâu│
│ trên app     │     │ xe gần nhất  │     │ chuyển tới   │     │ hoặc hủy cuốc│
│              │     │ theo GPS     │     │ khu vực đông │     │              │
│ Ai: System   │     │ Ai: System   │     │ khách        │     │ Ai: Customer │
│ ⏱ 1 phút     │     │ ⏱ 1 phút     │    │ ⏱ 5-10 phút 🔴│     ⏱ 3 phút 🔴  │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                              🔄
                     Handoff giữa hệ thống
                     và tài xế

🔴 = Bottleneck
⏱ Tổng thời gian xử lý trung bình: 10-15 phút/chuyến.
```

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

## 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Đội điều phối vận hành Xanh SM và tài xế Xanh SM. |
| **2. Current Workflow** | Khi khách đặt xe, hệ thống sẽ tìm tài xế gần nhất theo GPS thời gian thực. Sau khi trả khách, tài xế tự di chuyển tới khu vực đông người dựa vào kinh nghiệm cá nhân thay vì dự đoán dữ liệu nhu cầu thực tế. |
| **3. Bottleneck** | Hệ thống chưa dự đoán chính xác nhu cầu khách theo thời tiết, giờ cao điểm hoặc sự kiện lớn, khiến tài xế tập trung sai khu vực và khách phải chờ lâu. |
| **4. Business Impact** | Tỷ lệ hủy chuyến giờ cao điểm tăng khoảng 15%, tài xế chạy rỗng nhiều gây lãng phí pin và giảm doanh thu vận hành của Xanh SM. |
| **5. Success Metric** | Giảm tỷ lệ hủy chuyến từ 15% xuống dưới 5% và giảm quãng đường chạy rỗng ít nhất 10%. |
| **6. Operational Boundary** | AI chỉ được phép gợi ý khu vực có nhu cầu cao cho tài xế. AI không được tự động ép tài xế nhận cuốc hoặc thay đổi lộ trình mà không có xác nhận từ tài xế và hệ thống điều phối. |

---

## 3.3. Future-State Flow & AI Fit

* **AI Fit:** [ ] Rule / State-Machine  [ ] LLM Feature  [x] Agentic Loop

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ 🔵 AI phân   │     │ 🔵 AI gợi ý  │     │ 🟢 Tài xế    │
│ Khách đặt xe │ --> │ tích thời    │ --> │ khu vực có   │ --> │ xác nhận và  │
│ trên app     │     │ tiết + GPS + │     │ nhu cầu cao  │     │ di chuyển    │
│              │     │ dữ liệu cũ   │     │ cho tài xế   │     │               │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                    │
                                                                    ▼
                                                             ↩️ Fallback:
                                                             Nếu AI dự đoán
                                                             sai hoặc thiếu
                                                             dữ liệu, hệ thống
                                                             quay về điều phối
                                                             GPS thông thường.
```

---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [X] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
Xanh SM đã có dữ liệu GPS, lịch sử đặt xe, thời tiết, vùng hoạt động tài xế và dữ liệu huỷ chuyến để phục vụ việc huấn luyện mô hình dự báo nhu cầu.
2. [X] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?
Hệ thống AI chỉ đóng vai trò gợi ý vùng đón khách tiềm năng, không tự động điều chuyển tài xế hay huỷ cuốc xe.
Nếu confidence score thấp dưới 0.7, hệ thống sẽ fallback về cơ chế điều phối GPS mặc định.
Điều phối viên vận hành vẫn là người phê duyệt cuối cùng (Human-in-the-loop).
3. [X] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?
Đội vận hành và tài xế đều có động lực cải thiện hiệu suất vì hệ thống hiện tại gây nhiều cuốc huỷ và quãng đường chạy rỗng.
Giải pháp AI được tích hợp dưới dạng “gợi ý hỗ trợ”, không thay đổi hoàn toàn workflow hiện có nên khả năng chấp nhận cao.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[X] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> *Bài toán điều phối xe theo nhu cầu thời gian thực là một vấn đề vận hành có tác động trực tiếp đến doanh thu và trải nghiệm khách hàng của Xanh SM. Hệ thống hiện tại chủ yếu điều phối dựa trên GPS gần nhất nên chưa phản ứng tốt với các yếu tố động như thời tiết, giờ cao điểm hoặc sự kiện đông người.

Nhóm đánh giá đây là bài toán phù hợp để triển khai AI Prototype vì:

Có dữ liệu lịch sử lớn và liên tục cập nhật.
Có thể đo hiệu quả rõ ràng bằng các metric thực tế như:
giảm tỷ lệ huỷ chuyến,
giảm quãng đường chạy rỗng,
tăng conversion rate của cuốc xe.
Rủi ro vận hành được kiểm soát tốt nhờ cơ chế fallback và Human-in-the-loop.
Kiến trúc triển khai theo hướng AI assistant hỗ trợ điều phối nên chi phí thử nghiệm ban đầu thấp hơn so với xây dựng fully autonomous dispatch system.

Nhóm đề xuất bắt đầu với phạm vi hẹp tại một khu vực trung tâm của Hà Nội trong khung giờ cao điểm để thu thập baseline trước khi mở rộng toàn hệ thống.