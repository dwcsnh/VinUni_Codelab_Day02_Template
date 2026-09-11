# 01 — Problem Scan & Quick Problem Cards (Vin Smart Future)

> **Học phần:** Lab 02 — AI Product Scoping (Vin Smart Future — Vingroup)  
> **Người thực hiện:** AI Product Engineer — Vin Smart Future  
> **Branch:** `vmhieu`  
> **Deliverable:** `01-problem-scan.md` (Hoàn thành Phase 1 SCAN & Phase 2 QUICK-ASSESS)

---

## 🏛️ Bối cảnh nhiệm vụ: Vin Smart Future

Trong vai trò là **AI Product Engineer** tại **Vin Smart Future**, mục tiêu là rà soát toàn diện quy trình vận hành thực tế tại các công ty thành viên của Vingroup (Xanh SM, VinFast, Vinhomes, Vinmec, Vinpearl) để xác định các điểm nghẽn (bottlenecks), tác vụ thủ công lặp lại và cơ hội nâng cấp bằng AI nhằm tối ưu hóa chi phí vận hành và nâng cao trải nghiệm khách hàng.

---

# 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội tối ưu hóa

Quét qua hoạt động vận hành của các công ty thành viên Vingroup dựa trên **4 Lenses cốt lõi**:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày, quy tắc tương đối cố định.
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên, gây chậm trễ SLA.
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hoặc xử lý ngôn ngữ tự nhiên còn chậm, thiếu ngữ cảnh, rập khuôn.
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng, đối tác hoặc nhân viên thực địa phàn nàn nhiều nhất.

---

### 🤖 AI Prompt ứng dụng trong Phase 1 (Partner Brainstorm):
```text
"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho các mảng VinFast, Xanh SM, Vinhomes, Vinmec, Vinpearl. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."
```
> **Đúc kết từ AI Partner:** AI hỗ trợ phân rã quy trình vận hành thành các mắt xích cụ thể; đặc biệt chỉ ra rằng các bài toán liên quan đến điều phối xe điện thời gian thực (Xanh SM) và tóm tắt hồ sơ y tế (Vinmec) có tỷ lệ lãng phí thời gian thủ công cao nhất (~15-30 phút/giao dịch).

---

### 📝 Bảng quét 5 bài toán thực tế (Problem Scan):

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Xanh SM (GSM)** | Tốn thời gian | Điều phối viên tiếp nhận và xử lý thủ công các báo cáo khẩn cấp từ tài xế về sự cố pin/sạc thực địa (mất 12-15 phút/lượt tra cứu trạm sạc VinFast còn trụ trống, xác định tọa độ GPS và soạn hướng dẫn). |
| 2 | **VinFast** | AI có thể tốt hơn | Khách hàng mô tả lỗi xe điện (tiếng kêu gầm, lỗi cảm biến, hao pin bất thường) bằng ngôn ngữ tự nhiên tiếng Việt qua App VinFast; hệ thống hiện tại chưa tự động phân loại mã lỗi kỹ thuật ban đầu để hướng dẫn an toàn và đặt lịch bảo dưỡng. |
| 3 | **Vinhomes** | Lặp lại | Ban quản lý tòa nhà phải đọc và phân loại thủ công hàng trăm phản ánh/khiếu nại mỗi ngày qua App Vinhomes Resident (mất nước, hỏng đèn hành lang, tiếng ồn thi công) để điều phối đến đúng đội kỹ thuật từng phân khu. |
| 4 | **Vinmec** | Pain từ người khác | Bác sĩ và điều dưỡng quá tải khi phải trích xuất thủ công các chỉ số xét nghiệm, chẩn đoán lâm sàng từ bệnh án điện tử để soạn thảo bản tóm tắt hồ sơ xuất viện (Discharge Summary) mất 20-30 phút/bệnh nhân. |
| 5 | **Vinpearl** | Tốn thời gian | Bộ phận vận hành và kinh doanh mất 25-35 phút đọc email đặt phòng theo đoàn (Group Booking) phức tạp từ các công ty lữ hành, đối chiếu thủ công quỹ phòng trống trên hệ thống PMS và draft lệnh báo giá. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn **top 3 bài toán** tiềm năng nhất từ danh sách trên để tiến hành đánh giá nhanh:
* **Card #1:** Xanh SM — Điều phối xử lý sự cố pin / trạm sạc thực địa
* **Card #2:** VinFast — Trợ lý phân loại sơ bộ lỗi xe điện từ ngôn ngữ tự nhiên
* **Card #4:** Vinmec — Trợ lý tự động hóa draft tóm tắt hồ sơ xuất viện (Discharge Summary)

---

### 🤖 AI Prompt ứng dụng trong Phase 2 (Stress-Test thẻ bài toán):
```text
"Đây là các thẻ bài toán vận hành tôi đề xuất cho Vin Smart Future: Card #1 (Xanh SM sự cố pin), Card #2 (VinFast phân loại lỗi xe), Card #4 (Vinmec tóm tắt xuất viện). Hãy đóng vai trò là một CFO và Trưởng phòng Vận hành cực kỳ khắt khe, chỉ ra cho tôi 3 điểm yếu về logic, metric, và giải thích vì sao rule-based code thông thường có thể giải quyết bài toán này tốt hơn là dùng AI."
```
> **Đúc kết phản biện từ AI:**
> 1. Với **Card #2 (VinFast)**: Rủi ro sai lệch kỹ thuật an toàn cao nếu AI chẩn đoán sai mã lỗi phanh/pin; cần nhiều dữ liệu telemetry hơn là chỉ dựa trên text của người dùng.
> 2. Với **Card #4 (Vinmec)**: Giá trị tiết kiệm thời gian lớn nhưng vướng rào cản pháp lý y khoa và bảo mật dữ liệu bệnh án HIPAA/Bộ Y Tế; bắt buộc 100% Bác sĩ ký duyệt.
> 3. Với **Card #1 (Xanh SM)**: Tính cấp thiết vận hành cao nhất (ảnh hưởng trực tiếp đến cuốc xe của tài xế và SLA của Xanh SM). Logic kết hợp trạm sạc + vị trí xe có thể tận dụng API, trong khi LLM xử lý xuất sắc khâu trích xuất ý định khẩn cấp và sinh thông điệp hướng dẫn rõ ràng.

---

### 🃏 Chi tiết 3 Quick Problem Cards

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Tài xế Xanh SM báo cáo sự cố sạc pin / cạn pin    │
│ giữa đường cần điều phối trạm sạc trống hoặc xe cứu hộ.     │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế Xanh SM (lo lỡ chuyến/chờ đợi), │
│ Điều phối viên Dispatcher (quá tải thao tác nhiều hệ thống).│
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Tài xế gọi tổng đài điều vận báo mức pin nguy cấp      │
│   → 2. Điều phối viên tra cứu thủ công vị trí GPS của xe     │
│   → 3. Mở hệ thống CMS tra cứu trạm sạc VinFast còn trụ     │
│   → 4. Soạn tin nhắn chỉ dẫn lộ trình gửi qua App tài xế    │
│   → 5. Liên hệ đội Mobile Charger nếu pin cạn kiệt (<5%)    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 12 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4            │
│ (Tự động nhận diện toạ độ/mức pin -> gợi ý trạm -> draft tin)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   "Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút;   │
│    100% sự cố pin <5% được kích hoạt xe cứu hộ kịp thời."   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Khách hàng mô tả lỗi xe điện bằng tiếng Việt trên  │
│ App VinFast, cần phân loại sơ bộ mã lỗi & hướng dẫn an toàn.│
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Khách hàng lái xe điện (lo sợ mất an   │
│ toàn), CSKH và Kỹ thuật viên xưởng dịch vụ VinFast.         │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách gửi mô tả hiện tượng lạ trên xe qua App VinFast   │
│   → 2. CSKH đọc văn bản, tra cứu cẩm nang kỹ thuật/mã DTC   │
│   → 3. CSKH hỏi kỹ thuật viên nếu hiện tượng phức tạp/hiếm  │
│   → 4. Soạn tin khuyến cáo an toàn và hướng dẫn đặt lịch    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 18 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 4            │
│ (Trích xuất triệu chứng, map sang mã lỗi & draft khuyến cáo)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   "Giảm thời gian tư vấn lỗi ban đầu từ 20 phút ──> under 2m│
│    Độ chính xác phân loại nhóm lỗi kỹ thuật đạt >85%."      │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #4                                       │
│                                                             │
│ Bài toán: Bác sĩ & điều dưỡng phải trích xuất thủ công dữ   │
│ liệu EHR để soạn thảo tóm tắt bệnh án xuất viện Vinmec.     │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Bác sĩ điều trị & điều dưỡng Vinmec    │
│ (quá tải hành chính), Bệnh nhân (chờ đợi xuất viện lâu).    │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Bác sĩ mở hồ sơ bệnh án điện tử (EHR) bệnh nhân        │
│   → 2. Rà soát kết quả xét nghiệm, chẩn đoán, thuốc đã dùng │
│   → 3. Tóm tắt diễn tiến điều trị trong đợt nằm viện        │
│   → 4. Gõ văn bản tóm tắt xuất viện bằng tiếng Việt dễ hiểu │
│   → 5. Bác sĩ ký duyệt bản cứng/số và trao tay bệnh nhân    │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 4 (⏱ 25 phút/lượt) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4         │
│ (Trích xuất chỉ số chính, tổng hợp diễn tiến & draft tóm tắt)│
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   "Giảm thời gian soạn tóm tắt xuất viện từ 25m ──> under 5m│
│    100% hồ sơ xuất viện phải qua Bác sĩ duyệt ký (HITL)."   │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 Quyết định lựa chọn bài toán cho Deep-Dive

**Lựa chọn chính thức:** **Quick Problem Card #1 — Xanh SM: Điều phối xử lý sự cố pin / trạm sạc thực địa (Smart Dispatching Co-pilot).**

### Lý do lựa chọn:
1. **Giá trị vận hành tức thì (Real-time ROI):** Tác động trực tiếp đến hàng trăm tài xế Xanh SM mỗi ngày, giảm thời gian chết của phương tiện (EV downtime) và tăng tỷ lệ hoàn thành cuốc xe.
2. **Ranh giới vận hành rõ ràng (Operational Boundary):** Dễ dàng thiết lập các ranh giới an toàn tuyệt đối (ví dụ: cấm đề xuất trạm xa khi pin < 5%, cấm tự gửi tin không qua duyệt HITL).
3. **Phù hợp kiến trúc kỹ thuật:** Kết hợp dữ liệu hệ thống (API tọa độ xe, API trạng thái trạm sạc) với năng lực suy luận và sinh phản hồi tự nhiên của LLM (Gemini 2.5 Flash).
