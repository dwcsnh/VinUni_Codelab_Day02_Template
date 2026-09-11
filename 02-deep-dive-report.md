# 02 — Deep-Dive Report: Xanh SM Smart Dispatching Co-pilot

> **Đơn vị:** Vin Smart Future (Vingroup)  
> **Dự án:** Trợ lý Điều phối Sự cố Pin Thực địa Xanh SM (GSM Intelligent Dispatcher)  
> **Phạm vi:** Lab 02 — AI Product Scoping  
> **Deliverable:** `02-deep-dive-report.md` (Hoàn thiện Phase 3 DEEP-DIVE & Phase 5 EVALUATE)

---

## 📌 Tổng quan lựa chọn bài toán

Từ kết quả phân tích tại **Phase 1 (Problem Scan)** và **Phase 2 (Quick Problem Cards)**, nhóm quyết định chọn:
**Quick Problem Card #1 — Xanh SM: Điều phối xử lý sự cố pin / trạm sạc thực địa.**

### Lý do lựa chọn và sàng lọc:
* **So với Card #2 (VinFast - Phân loại lỗi xe):** Việc chẩn đoán lỗi phần cứng/an toàn xe điện chỉ qua mô tả ngôn ngữ tự nhiên tiềm ẩn rủi ro tai nạn nghiêm trọng nếu AI nhận định sai; cần hệ thống Telemetry/OBD-II trực tiếp từ xe trước khi áp dụng AI đàm thoại.
* **So với Card #4 (Vinmec - Tóm tắt bệnh án xuất viện):** Dù mang lại giá trị giải phóng sức lao động lớn cho y bác sĩ, thủ tục phê duyệt pháp lý y khoa và tích hợp dữ liệu bảo mật bệnh án đòi hỏi chu kỳ chuẩn bị dài hạn (6-12 tháng).
* **Card #1 (Xanh SM):** Cực kỳ cấp thiết, đo lường được ngay bằng thời gian xe dừng đỗ (EV downtime), doanh thu bị rò rỉ và khối lượng công việc của điều phối viên. Ranh giới vận hành hoàn toàn có thể kiểm soát chặt chẽ với cơ chế **Human-in-the-loop (HITL)**.

---

# 🏗️ Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping

Quy trình vận hành thủ công hiện tại khi tài xế Xanh SM gặp sự cố pin/sạc trên đường:

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3 🔴       │
│ Nhận cuộc gọi / │ ────> │ Tra cứu định vị │ ────> │ Tra cứu trạm    │
│ tin nhắn sự cố  │  🔄   │ GPS phương tiện │  🔄   │ sạc VinFast     │
│                 │       │                 │       │ còn trụ trống   │
│ Actor: Dispatch │       │ Actor: Dispatch │       │ Actor: Dispatch │
│ ⏱ 2 phút        │       │ ⏱ 2 phút        │       │ ⏱ 5 phút (Chậm) │
│ In: SĐT tài xế  │       │ In: Biển số xe  │       │ In: Toạ độ GPS  │
│ Out: Ticket log │       │ Out: Toạ độ     │       │ Out: Địa chỉ    │
└─────────────────┘       └─────────────────┘       └─────────────────┘
                                                             │
                                                             ▼
┌─────────────────┐                                 ┌─────────────────┐
│ Bước 5          │                                 │ Bước 4 🔴       │
│ Gọi xe sạc pin  │ <────────────────────────────── │ Soạn tin nhắn   │
│ lưu động (nếu   │                🔄               │ hướng dẫn gửi   │
│ pin cạn < 5%)   │                                 │ cho tài xế      │
│ Actor: Dispatch │                                 │ Actor: Dispatch │
│ ⏱ 1 phút        │                                 │ ⏱ 5 phút (Lỗi)  │
│ In: Tình trạng  │                                 │ In: Trạm sạc    │
│ Out: Lệnh cứu hộ│                                 │ Out: SMS / App  │
└─────────────────┘                                 └─────────────────┘

Ký hiệu:
🔴 Bottlenecks (Bước 3 & Bước 4 tiêu tốn 10/15 phút xử lý)
🔄 Handoffs (Chuyển giao thông tin giữa các màn hình CMS độc lập)
⏱ Tổng thời gian xử lý thủ công: ~15 phút/lượt
```

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) tại Trung tâm Điều vận Xanh SM (GSM) khu vực Hà Nội & TP.HCM. |
| **2. Current Workflow** | Khi tài xế báo sự cố hết pin/lỗi trạm, Dispatcher chuyển đổi qua lại giữa 3 hệ thống: App tài xế, Bản đồ định vị GPS nội bộ, và Dashboard quản trị trạm sạc VinFast để tìm trụ sạc tương thích còn trống; sau đó tự gõ tin nhắn chỉ đường gửi tài xế. Mất trung bình 15 phút/lượt. |
| **3. Bottleneck** | **Bước 3 & Bước 4:** Tra cứu chéo trụ sạc trống theo đúng chuẩn cổng sạc của dòng xe (VF5/VFe34/VF8) và gõ tin nhắn hướng dẫn lộ trình bằng tay trong điều kiện áp lực cao vào giờ cao điểm. |
| **4. Business Impact** | Mỗi ngày có trung bình 70-90 sự cố pin thực địa tại các thành phố lớn. Tiêu tốn hơn 20 giờ lao động/ngày của đội ngũ Dispatcher. Xe điện nằm chờ khiến tài xế lỡ cuốc, gây rò rỉ ước tính 12-15% doanh thu ca xe và làm suy giảm NPS của dịch vụ taxi Xanh SM. |
| **5. Success Metric** | 1. **Hiệu suất (Efficiency):** Giảm tổng thời gian xử lý sự cố từ **15 phút ──> dưới 3 phút/lượt**.<br>2. **Độ chính xác (Quality):** Tỷ lệ gợi ý trạm sạc chính xác (còn trụ trống, đúng chuẩn sạc xe, khoảng cách tối ưu) đạt **≥ 98%**.<br>3. **An toàn (Safety):** 100% trường hợp pin khẩn cấp (< 5%) được kích hoạt đề xuất xe sạc di động (Mobile Charger), không để xe chết máy trên đường. |
| **6. Operational Boundary** | **ĐƯỢC PHÉP:** Tự động truy xuất toạ độ GPS, tình trạng trạm sạc qua API nội bộ; phân tích mức pin; sinh dự thảo tin nhắn hướng dẫn với tiền tố bắt buộc `[DRAFT_ONLY]`.<br>**TUYỆT ĐỐI CẤM:** Không được tự động bắn tin nhắn trực tiếp đến tài xế mà chưa qua Dispatcher xác nhận (bắt buộc Human-in-the-loop); cấm gợi ý trạm sạc > 5km khi pin dưới 5%. |

---

### 🤖 AI Prompt ứng dụng trong Phase 3 (Thiết kế ranh giới & Luồng xử lý):
```text
"Tôi đang xây dựng Problem Statement 6-field cho dự án Smart Dispatching Co-pilot của Xanh SM. Hãy phản biện các điểm nghẽn và đưa ra các ranh giới vận hành (Operational Boundaries) khắc nghiệt nhất để ngăn chặn rủi ro tài xế xe điện bị dẫn đến trạm sạc hỏng hoặc trạm sạc quá xa khi pin sắp cạn."
```
> **Đúc kết từ AI Partner:** Cần bổ sung quy tắc cứng (Hard Constraint): Nếu SoC (State of Charge) < 5%, loại bỏ hoàn toàn phương án hướng dẫn tài xế tự lái đi sạc trên 5km; lập tức chuyển sang chế độ kích hoạt xe sạc lưu động để đảm bảo an toàn giao thông.

---

## 3.3. Future-State Flow & AI Fit

### Phân tích AI-Fit Matrix:
* **Lựa chọn:** **LLM Feature (Copilot)** kết hợp **Deterministic Rules (State-Machine API)**.
* **Lý do không dùng Full Agentic Loop:** Việc điều phối cứu hộ giao thông có rủi ro thực tế cao. Đặt một Agent tự trị hoàn toàn có thể gây ra ảo giác dẫn xe vào ngõ cụt hoặc trạm sạc đang bảo trì. Mô hình Co-pilot (Hỗ trợ soạn nháp + Con người bấm duyệt) đảm bảo an toàn tuyệt đối và tính trách nhiệm giải trình.

### Sơ đồ quy trình tương lai (Future-State Flow):

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │
│ Nhận cuộc gọi / │ ────> │ 🔵 Auto-fetch    │ ────> │ 🔵 LLM Co-pilot │
│ tin nhắn sự cố  │       │ Telemetry & CMS │       │ Sinh nội dung   │
│                 │       │ - Tọa độ xe     │       │ [DRAFT_ONLY]    │
│ Actor: Dispatch │       │ - Mức pin (%)   │       │ đề xuất trạm    │
│ ⏱ 30 giây       │       │ - Trạm khả dụng │       │ ⏱ 5 giây        │
└─────────────────┘       └─────────────────┘       └─────────────────┘
                                                             │
                                                             ▼
┌─────────────────┐                                 ┌─────────────────┐
│ ↩️ Fallback Plan │                                 │ Bước 4          │
│ Nếu LLM lỗi /   │ <────────────────────────────── │ 🟢 Human Review │
│ không khả dụng, │            [Reject /            │ Dispatcher xem, │
│ Dispatcher gõ   │             Error]              │ xác nhận & gửi  │
│ thủ công như cũ │                                 │ ⏱ 30 giây       │
└─────────────────┘                                 └─────────────────┘

Ký hiệu:
🔵 AI Step: Tác vụ tự động hóa API và LLM xử lý trong vài giây.
🟢 Human Step (HITL): Điểm phê duyệt bắt buộc của Dispatcher trước khi gửi tin.
↩️ Fallback: Kịch bản dự phòng khi AI không tự tin hoặc hệ thống lỗi.
⏱ Tổng thời gian quy trình mới: ~1.5 - 2 phút/lượt (Giảm > 85% thời gian).
```

---

# 💻 Phase 4 — Liên kết Thử nghiệm Kỹ thuật (Prompt Prototype)

Quy định vận hành của hệ thống được kiểm chứng trực tiếp bằng mã nguồn Python `starter-code/prompt_prototype.py` với **Gemini 2.5 Flash**:
1. **Quy tắc 1 (Tagging an toàn):** Mọi phản hồi dạng nháp gửi tài xế phải bắt đầu bằng `[DRAFT_ONLY]`.
2. **Quy tắc 2 (Ngưỡng pin nguy cấp):** Khi mức pin xe `< 5%`, mô hình bị nghiêm cấm đề xuất trạm sạc cách xa quá 5km, mà phải trả về payload JSON hành động điều phối cứu hộ: `{"action": "dispatch_mobile_charger", "reason": "..."}`.
3. **Thử nghiệm đối kháng (Adversarial Robustness):** Các prompt cố tình dụ AI "bỏ qua bước nháp" hoặc "ép gửi trạm xa khi pin 2%" đều bị chặn đứng và xử lý đúng quy chuẩn an toàn.

---

# 🏁 Phase 5 — EVALUATE

## 5.1. AI Readiness Checklist

| Tiêu chí đánh giá | Trạng thái | Ghi chú minh chứng |
|---|:---:|---|
| **1. Dữ liệu mẫu/logs sạch sẵn có?** | ✅ ĐẠT | Xanh SM và VinFast đã đồng bộ hệ thống Telemetry xe điện và API tình trạng thời gian thực của mạng lưới cổng sạc VinFast. |
| **2. Rủi ro sai sót trong tầm kiểm soát?** | ✅ ĐẠT | Quy trình thiết lập cơ chế **HITL (Human-in-the-loop)**: Dispatcher luôn là người đọc lại và bấm nút "Gửi". Có fallback thủ công ngay lập tức. |
| **3. Stakeholders sẵn sàng thay đổi?** | ✅ ĐẠT | Đội ngũ Điều phối viên đang chịu áp lực quá tải rất lớn trong giờ cao điểm; giải pháp giúp họ giảm 80% thao tác gõ lặp lại nên nhận được sự đồng thuận cao. |

---

### 🤖 AI Prompt ứng dụng trong Phase 5 (Phản biện quyết định đầu tư):
```text
"Hãy đóng vai trò Giám đốc Khối Công nghệ Vin Smart Future, hãy đánh giá xem dự án Xanh SM Smart Dispatching Co-pilot có nên bấm nút GO triển khai thực tế hay không? Những rủi ro lớn nhất về mặt kỹ thuật và chi phí vận hành API là gì?"
```
> **Đúc kết từ AI Partner:** Dự án có tính khả thi cực cao vì scope hẹp, latency của Gemini 2.5 Flash rất thấp (~1-2s), chi phí token không đáng kể so với giá trị tiết kiệm giờ công lao động của hàng trăm điều phối viên.

---

## 5.2. Quyết định của Ban Giám Đốc Vin Smart Future

* [x] **GO (Bắt đầu xây dựng Prototype & Triển khai Pilot hẹp)**
* [ ] **NOT YET (Cần tích lũy thêm dữ liệu / xác lập baseline)**
* [ ] **NO-GO (Không khả thi / Rule-based tốt hơn)**

### 📝 Lý giải quyết định (Justification):
1. **Giá trị kinh tế & Vận hành rõ rệt:** Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút giúp đội ngũ điều vận Xanh SM xử lý nhanh gấp 5 lần lượng sự cố pin trong giờ cao điểm mà không cần tăng biên chế nhân sự.
2. **Kiến trúc khả thi và chi phí thấp:** Không xây dựng hệ sinh thái Agent phức tạp mà sử dụng kiến trúc **LLM Feature** gọn nhẹ, tận dụng API có sẵn của trạm sạc VinFast.
3. **Ranh giới an toàn tuyệt đối:** Có quy tắc cứng cho trường hợp pin nguy cấp (<5%) và giữ vững chốt chặn con người (HITL) trước mọi thông điệp gửi tới tài xế.
