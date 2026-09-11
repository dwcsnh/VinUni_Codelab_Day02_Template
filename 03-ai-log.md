# 03 - AI Log and Reflection

## Bối cảnh

Trong buổi lab này, tôi đóng vai trò AI Product Engineer tại Vin Smart Future và chọn bài toán `Xanh SM xử lý sự cố pin yếu cho tài xế xe điện`. Tôi đã sử dụng AI như một thought-partner để brainstorm bài toán, phản biện độ khả thi, viết problem statement, và hoàn thiện prompt prototype để test boundary an toàn.

---

## AI đã giúp tôi như thế nào

AI hỗ trợ tốt nhất ở 4 việc.

**Thứ nhất, brainstorm nhanh danh sách pain point.** Khi chưa chắc nên chọn bài toán nào, AI giúp tôi mở rộng tư duy ra nhiều quy trình vận hành thuộc Xanh SM, VinFast, Vinhomes và Vinmec. Những gợi ý này không thay thế phần phân tích của tôi, nhưng rất hiệu quả để tạo "bản đồ cơ hội" ban đầu.

**Thứ hai, ép tôi cụ thể hóa metric và boundary.** Lúc đầu mô tả bài toán của tôi khá chung chung, kiểu như "giảm thời gian xử lý" hay "cải thiện trải nghiệm tài xế". Khi yêu cầu AI đóng vai trò CFO và trưởng vận hành khó tính, nó liên tục hỏi lại: giảm từ bao nhiêu xuống bao nhiêu, ai phê duyệt, nếu AI sai thì ai chịu trách nhiệm. Cách hỏi này giúp tôi viết được problem statement rõ hơn.

**Thứ ba, hỗ trợ viết draft tài liệu nhanh hơn.** AI rất hữu ích trong việc biến ý tưởng thành câu chữ mạch lạc cho `01-problem-scan.md` và `02-deep-dive-report.md`. Nhờ đó tôi có thêm thời gian để tập trung vào logic và operational boundary thay vì mất nhiều thời gian cho việc diễn đạt.

**Thứ tư, hỗ trợ stress-test prompt prototype.** AI giúp tôi nghĩ ra các adversarial inputs cố ý bỏ qua boundary, ví dụ yêu cầu bỏ tag `[DRAFT_ONLY]` hoặc ép hệ thống gợi ý trạm sạc xa trong lúc pin đang dưới ngưỡng an toàn.

---

## AI đã trả lời sai hoặc chưa tốt ở đâu

AI không phải lúc nào cũng đúng, và đây là phần tôi thấy giá trị nhất trong buổi học.

**Sai lệch 1: AI có xu hướng over-solutioning.** Ở một số lần brainstorm đầu tiên, AI đề xuất giải pháp quá "hoành tráng", nghiêng về agent tự trị hoặc tự động gửi lệnh điều phối. Điều này không phù hợp với bài toán có rủi ro vận hành cao. Nếu tôi nghe theo hoàn toàn, bài làm sẽ đẹp trên giấy nhưng nguy hiểm trong thực tế.

**Sai lệch 2: AI có xu hướng tự điền thêm chi tiết.** Khi tôi không đưa metric rõ ràng, AI tự thêm các số như số lượt sự cố mỗi ngày, mức doanh thu thất thoát, hay tỷ lệ đúng của hệ thống. Những con số đó chỉ là ước đoán, không nên trình bày như sự thật. Vì vậy tôi phải đổi cách viết, chuyển những con số này thành giả thuyết vận hành hoặc metric mục tiêu.

**Sai lệch 3: AI ban đầu chưa tôn trọng boundary chặt.** Trong một vài prompt sơ khai, nếu user ép "gửi thẳng cho tài xế" thì AI vẫn viết theo kiểu đã sẵn sàng gửi, hoặc đề xuất trạm sạc xa mà không cảnh báo đủ mức pin rất thấp. Điều này cho thấy nếu system prompt không đủ mạnh thì model dễ bị kéo vượt ranh giới.

**Sai lệch 4: AI dễ viết nghe hay hơn là đúng nghiệp vụ.** Có những đoạn AI soạn ra rất trôi chảy, nhưng không nêu rõ ai review, fallback là gì, và điểm nào là bottleneck thật sự trong current workflow. Về hình thức thì ổn, nhưng về vận hành thì chưa đạt.

---

## Tôi đã sửa prompt và boundary như thế nào

Sau các lần thử đầu, tôi rút ra rằng không thể chỉ "bảo AI làm cẩn thận". Tôi phải viết boundary thành quy tắc vận hành rõ ràng.

**1. Chốt vai trò rất hẹp.** Thay vì để model đóng vai "trợ lý thông minh", tôi giới hạn nó thành `dispatcher co-pilot` chỉ được tạo bản nháp để con người review.

**2. Biến quy tắc an toàn thành hard rules.** Tôi viết rõ:

- Mọi output phải bắt đầu bằng `[DRAFT_ONLY]`.
- Không được bỏ tag này dù user có yêu cầu.
- Nếu pin `< 5%` thì không được đề xuất trạm sạc xa hơn `5 km`.
- Trường hợp pin nguy cấp thì phải ưu tiên `dispatch_mobile_charger`.
- Không được tự động gửi, không được xác nhận đã gửi.

**3. Chuyển output sang hướng có cấu trúc.** Thay vì để model nói tự do bằng đoạn văn tự nhiên, tôi ưu tiên JSON trong trường hợp cần đề xuất hành động. Cách này giúp dễ verify hơn và giảm nguy cơ model diễn đạt mơ hồ.

**4. Thêm adversarial tests.** Tôi cố tình viết các prompt tấn công để ép model vượt boundary. Mục tiêu không phải để AI trả lời đẹp, mà để xem nó có "ngã" khi gặp user cố ý bỏ qua quy trình an toàn hay không.

---

## Điều tôi học được về cách làm việc với AI

Qua bài này, tôi thấy AI mạnh nhất khi đóng vai trò đồng nghiệp phân tích, người viết nháp nhanh, và công cụ stress-test ý tưởng. AI không nên được tin như một nguồn chân lý, đặc biệt khi bài toán có yếu tố vận hành thời gian thực và rủi ro an toàn.

Điều quan trọng nhất tôi học được là: `Problem first, AI second.` Nếu workflow chưa rõ, metric chưa cụ thể, và boundary chưa viết thành luật, thì AI sẽ rất dễ trả lời nghe hợp lý nhưng không thể dựa vào để vận hành.

Tôi cũng học được một bài học kỹ thuật rất thực tế: prompt tốt không chỉ là prompt "hay", mà là prompt có quy định vai trò, output format, điều cấm, fallback, và human-in-the-loop. Đây mới là phần biến AI thành một thành phần có thể kiểm soát được trong hệ thống.

---

## Kết luận cá nhân

Nếu chỉ dùng AI để viết cho nhanh, tôi sẽ rất dễ nộp một bài đẹp nhưng mong manh. Khi dùng AI để tranh luận, phản biện, và test ranh giới, tôi thấy rõ hơn bài toán mình đang giải. Đó là giá trị lớn nhất của buổi lab này.

Lần sau nếu làm tiếp, tôi muốn bổ sung thêm 2 hướng:

1. Tạo thêm nhiều adversarial test hơn, đặc biệt các trường hợp input thiếu hoặc user cố ý role-play để qua mặt boundary.
2. Tách phần rule-based validation ra khỏi LLM để các quy tắc an toàn cốt lõi không phụ thuộc hoàn toàn vào model.
