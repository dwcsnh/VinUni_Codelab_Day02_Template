# 02 - Deep Dive Report

## Dự án được chọn

**Vin Smart Future x Xanh SM:** Hỗ trợ điều phối xử lý sự cố pin yếu cho tài xế xe điện trong thời gian thực.

---

## Phase 3 - DEEP-DIVE

## 3.1 Current-State Workflow Mapping

### Quy trình hiện tại

```text
Tài xế báo sự cố pin
    |
    v
[1] Gọi tổng đài điều vận
    - Actor: Tài xế
    - Thời gian: 1-2 phút
    |
    v
[2] Dispatcher xác minh biển số, dòng xe, mức pin, vị trí GPS
    - Actor: Điều phối viên
    - Thời gian: 2 phút
    - Handoff: Từ cuộc gọi sang hệ thống nội bộ
    |
    v
[3] Dispatcher mở dashboard trạm sạc để tìm điểm sạc phù hợp
    - Actor: Điều phối viên
    - Thời gian: 4-5 phút
    - Bottleneck: Phải tự so sánh khoảng cách, tình trạng chỗ trống, loại cổng sạc
    |
    v
[4] Dispatcher tự soạn tin nhắn hướng dẫn cho tài xế
    - Actor: Điều phối viên
    - Thời gian: 4-5 phút
    - Bottleneck: Dễ sai địa chỉ, sai ngữ cảnh, mất thời gian
    |
    v
[5] Nếu pin quá thấp thì liên hệ xe cứu hộ hoặc sạc di động
    - Actor: Điều phối viên
    - Thời gian: 1-2 phút
    - Handoff: Sang đội cứu hộ/hỗ trợ hiện trường
```

**Tổng thời gian trung bình:** 12-16 phút/lượt.

### Bottleneck chính

- Bước `[3]` chậm vì dispatcher phải đối chiếu nhiều nguồn thông tin thủ công.
- Bước `[4]` chậm vì cần viết thông điệp rõ ràng, an toàn, phù hợp ngữ cảnh khẩn cấp.
- Rủi ro lớn nhất là gợi ý trạm quá xa khi pin còn quá thấp, làm xe không đến được trạm.

---

## 3.2 Problem Statement (6-field) & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên của Trung tâm Điều vận Xanh SM; người nhận thông tin sự cố và đưa hướng xử lý cho tài xế. |
| **2. Current Workflow** | Sau khi nhận cuộc gọi, dispatcher tra cứu GPS, xác định dòng xe, kiểm tra mức pin, tìm trạm sạc phù hợp, rồi tự soạn tin nhắn hướng dẫn hoặc gọi đội cứu hộ. Mọi thao tác hiện nay gần như làm thủ công trên nhiều màn hình. |
| **3. Bottleneck** | Tra cứu trạm sạc phù hợp và soạn thông điệp hướng dẫn là hai bước chậm nhất, chiếm khoảng 8-10 phút trong tổng quy trình. Đây cũng là nơi dễ xảy ra sai sót về khoảng cách, loại cổng sạc và ngữ cảnh an toàn. |
| **4. Business Impact** | Trong giờ cao điểm, mỗi sự cố pin yếu làm mất 12-16 phút của dispatcher, tăng thời gian xe ngoài khai thác, có nguy cơ hủy cuốc hoặc chậm đón khách tiếp theo. Nếu xử lý sai, doanh thu chuyến đi và trải nghiệm tài xế đều bị ảnh hưởng. |
| **5. Success Metric** | 1. 90% sự cố có draft hướng xử lý dưới 60 giây. 2. Giảm tổng thời gian xử lý từ 15 phút xuống dưới 3 phút. 3. 98% draft đúng boundary an toàn liên quan mức pin và khoảng cách trạm sạc. |
| **6. Operational Boundary** | AI chỉ được phép đọc dữ liệu cấu trúc, gợi ý phương án và sinh bản nháp gửi tài xế. AI không được tự động gửi tin, không được đề xuất trạm sạc xa hơn 5 km khi pin dưới 5%, không được bỏ qua bước human review, và không được tự ý bịa thêm dữ liệu không có trong input. |

---

## 3.3 Future-State Flow & AI Fit

### AI Fit Matrix

- Lựa chọn đề xuất: `LLM Feature`
- Không chọn `Rule only` vì thông điệp cần diễn đạt tự nhiên, rõ ràng, có thể thay đổi theo ngữ cảnh.
- Chưa cần `Agentic Loop` vì workflow ngắn, có biến đầu vào rõ, và rủi ro cao nếu để hệ thống tự trị hành động.

### Future-State Flow

```text
[1] Tài xế báo sự cố
    |
    v
[2] Hệ thống tự động nạp dữ liệu cấu trúc
    - GPS
    - Dòng xe
    - Mức pin
    - Danh sách trạm sạc khả dụng
    |
    v
[3] AI Step
    - Kiểm tra boundary an toàn
    - Nếu pin < 5% => draft JSON dispatch_mobile_charger
    - Nếu pin an toàn => draft hướng dẫn đến trạm phù hợp
    |
    v
[4] Human Step (HITL)
    - Dispatcher xem bản nháp
    - Sửa nếu cần
    - Bấm duyệt gửi
    |
    v
[5] Fallback
    - Nếu AI không chắc chắn, output lỗi, hoặc dữ liệu thiếu
    - Dispatcher quay lại cách làm thủ công hiện tại
```

### Vai trò của AI trong quy trình mới

- Giảm thao tác tìm kiếm và tổng hợp thông tin.
- Chuyển giai đoạn soạn thông điệp thành "draft-first" thay vì "viết tay từ đầu".
- Thực thi boundary an toàn nhất quán thông qua system prompt và output format có kiểm soát.

### Human-in-the-loop

- Dispatcher vẫn là người phê duyệt cuối cùng.
- Mọi thông điệp gửi tài xế phải đi qua bước review.
- Trường hợp pin rất thấp, dispatcher xác nhận điều xe sạc di động thay vì để AI tự quyết hành động thực tế.

### Fallback plan

- Nếu API trạm sạc lỗi: dispatcher tra cứu dashboard thủ công.
- Nếu AI trả về output sai schema: bỏ qua AI và xử lý thủ công.
- Nếu confidence thấp hoặc input thiếu: yêu cầu bổ sung thông tin trước khi draft.

---

## Phase 5 - EVALUATE

## AI Readiness Checklist

1. [x] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
2. [x] Rủi ro khi AI sai nằm trong tầm kiểm soát nhờ HITL và fallback?
3. [x] Stakeholders sẵn sàng thay đổi quy trình cũ?

## Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future

- [x] **GO (Bắt đầu xây dựng Prototype)**
- [ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline)**
- [ ] **NO-GO (Không khả thi / Rule-based tốt hơn)**

## Justification

Dự án này nên được xếp mức **GO** vì bài toán có scope hẹp, đầu vào rõ ràng, metric đo được ngay trong vận hành, và có thể triển khai theo kiểu có kiểm duyệt của con người. Giá trị thu được là giảm thời gian xử lý sự cố pin, giảm áp lực cho dispatcher, và hạn chế tình huống tài xế nhận hướng dẫn không an toàn.

Về kỹ thuật, đây là bài toán phù hợp để bắt đầu bằng `LLM Feature` thay vì xây agent phức tạp. Phần "tìm quyết định an toàn" có thể được cố định bằng boundary rất rõ: pin dưới 5% thì không gợi ý trạm xa, ưu tiên `dispatch_mobile_charger`. Phần "diễn đạt thông điệp" là nơi LLM tạo giá trị rõ nhất vì rule-based code thường khó viết hết các biến thể ngôn ngữ tự nhiên.

Về vận hành, rủi ro đã được kiểm soát bằng 3 lớp:

- Không auto-send, mọi output chỉ là draft.
- Dispatcher review trước khi gửi.
- Có fallback quay lại quy trình thủ công nếu AI lỗi hoặc thiếu dữ liệu.

Trong pha prototype, nhóm sẽ ưu tiên test boundary, tốc độ, và độ đúng output schema trước khi nghĩ đến tích hợp thực tế vào hệ thống điều vận.
