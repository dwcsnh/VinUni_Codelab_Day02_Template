# 01 - Problem Scan

## Phase 1 - SCAN

### Danh sách 5 bài toán vận hành tiềm năng

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | Xanh SM | Repetitive | Điều phối viên xử lý thủ công các cuộc gọi báo pin yếu, phải tra GPS, tra trạm sạc, rồi soạn tin nhắn hướng dẫn cho tài xế. |
| 2 | Xanh SM | Time-consuming | Tổng hợp lý do khách hủy chuyến từ ghi chú, log cuộc gọi và app để tìm mẫu lỗi vận hành mất nhiều giờ mỗi ngày. |
| 3 | VinFast | Repetitive | Đội vận hành phải đối chiếu log sạc giữa xe, trạm sạc và đối tác thanh toán để phát hiện sai lệch hóa đơn. |
| 4 | Vinhomes | AI-upgrade | Bộ phận CSKH phân loại khiếu nại cư dân và soạn phản hồi ban đầu còn chậm, dễ trễ SLA và dễ trả lời lặp lại. |
| 5 | Vinmec | Stakeholder Pain | Bác sĩ tốn nhiều thời gian viết tóm tắt hồ sơ xuất viện, gây quá tải và chậm quay vòng giường bệnh. |

### Nhận xét nhanh từ Phase 1

- Bài toán #1 có tác động vận hành thời gian thực, có đầu vào rõ ràng và phù hợp để dùng LLM ở vai trò draft có kiểm duyệt.
- Bài toán #4 và #5 hấp dẫn nhưng rủi ro pháp lý và chuyên môn cao hơn, cần boundary chặt và dữ liệu sạch hơn trước khi prototype.
- Bài toán #2 phù hợp cho phân tích offline, có giá trị nhưng độ cấp bách thấp hơn #1.

---

## Phase 2 - QUICK-ASSESS

### Top 3 bài toán được chọn

1. Xanh SM - Xử lý sự cố pin yếu hoặc thiếu pin giữa đường.
2. Vinhomes - Phân loại khiếu nại và draft phản hồi cư dân.
3. Vinmec - Tóm tắt hồ sơ xuất viện cho bác sĩ.

## Quick Problem Card #1

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                      │
│                                                             │
│ Bài toán (1 câu): Khi tài xế Xanh SM báo pin yếu giữa      │
│ đường, điều phối viên phải nhanh chóng xác định phương án  │
│ an toàn và soạn hướng dẫn gửi tài xế.                      │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes │
│                     [ ] Vinmec   [ ] Khác                  │
│                                                             │
│ Ai đang đau (Actor)? Điều phối viên và tài xế Xanh SM      │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Tài xế gọi tổng đài báo pin yếu                       │
│   2. Điều phối viên tra cứu GPS và dòng xe                 │
│   3. Tra cứu trạm sạc khả dụng gần nhất                    │
│   4. Soạn tin nhắn hướng dẫn hoặc điều xe cứu hộ           │
│   5. Chờ tài xế xác nhận và tiếp tục cập nhật              │
│                                                             │
│ Bước tốn thời gian/lỗi nhất? Bước 3-4 (10-12 phút/lượt)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                      │
│ Giảm thời gian xử lý sự cố từ 15 phút xuống dưới 3 phút.   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent│
└─────────────────────────────────────────────────────────────┘
```

## Quick Problem Card #2

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                      │
│                                                             │
│ Bài toán (1 câu): CSKH Vinhomes cần phân loại nhanh nội    │
│ dung khiếu nại và draft phản hồi ban đầu để giữ SLA.       │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes │
│                     [ ] Vinmec   [ ] Khác                  │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH và cư dân              │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Nhận ticket từ app cư dân                             │
│   2. Đọc nội dung và file đính kèm                         │
│   3. Tự phân loại mức độ khẩn cấp                          │
│   4. Chuyển ticket tới bộ phận liên quan                   │
│   5. Soạn phản hồi ban đầu gửi cư dân                      │
│                                                             │
│ Bước tốn thời gian/lỗi nhất? Bước 2-5 (8-10 phút/vé)       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 và 5       │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                      │
│ 85% ticket được phân loại đúng và draft dưới 60 giây.      │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent│
└─────────────────────────────────────────────────────────────┘
```

## Quick Problem Card #3

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                      │
│                                                             │
│ Bài toán (1 câu): Bác sĩ Vinmec tốn nhiều thời gian viết   │
│ tóm tắt xuất viện từ hồ sơ bệnh án dài và nhiều định dạng. │
│                                                             │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes │
│                     [x] Vinmec   [ ] Khác                  │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ điều trị và điều dưỡng         │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                     │
│   1. Mở bệnh án điện tử                                    │
│   2. Đọc diễn biến điều trị, thuốc và cận lâm sàng         │
│   3. Tổng hợp nội dung cần đưa vào giấy ra viện            │
│   4. Tự viết bản tóm tắt                                   │
│   5. Chỉnh sửa sau khi bị nhắc thiếu thông tin             │
│                                                             │
│ Bước tốn thời gian/lỗi nhất? Bước 2-4 (20-30 phút/ca)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3-4             │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                      │
│ Giảm thời gian tạo tóm tắt xuất viện xuống dưới 5 phút.    │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent│
└─────────────────────────────────────────────────────────────┘
```

---

## Bài toán được đề xuất để deep-dive

Nhóm đề xuất chọn bài toán `Xanh SM - Xử lý sự cố pin yếu hoặc thiếu pin giữa đường`.

### Lý do chọn

- Đầu vào có cấu trúc rõ ràng: mức pin, vị trí GPS, dòng xe, danh sách trạm sạc khả dụng.
- Giá trị vận hành dễ thấy bằng metric cụ thể: thời gian xử lý, tỷ lệ hướng dẫn đúng, tỷ lệ cần cứu hộ.
- Có thể prototype nhanh bằng prompt + JSON output + human review.
- Rất phù hợp với bài `prompt_prototype.py` vì boundary an toàn đã được định nghĩa sẵn.

### Lý do chưa chọn 2 bài toán còn lại

- `Vinhomes CSKH`: cần chuẩn hóa taxonomy ticket và boundary liên quan cam kết dịch vụ, bồi hoàn, tranh chấp.
- `Vinmec xuất viện`: rủi ro chuyên môn và bảo mật dữ liệu cao hơn, không phù hợp để làm prompt prototype nhỏ trong một buổi lab.
