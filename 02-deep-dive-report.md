# Lab 02 — Deep-Dive Report: Tối Ưu Xử Lý Khiếu Nại Cước Xanh SM

> **Nhóm:** Team Fast
> **Thành viên:**
>
> - [Nguyễn Minh Chiến] — [2A202600664]
> - [Vũ Tuấn Phương] — [2A202600772]
> - [Lê Quang Miền] — [2A202600715]
> - [Nguyễn Hải Quân] - [2A202600660]

---

# 🏗️ Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow Mapping

Quy trình xử lý khiếu nại cước phí hiện tại của nhân viên CSKH Xanh SM:

| Bước                | Mô tả                                                                                         | Thời gian | Đầu vào                | Đầu ra               | Vấn đề                                                                       |
| ------------------- | --------------------------------------------------------------------------------------------- | --------- | ---------------------- | -------------------- | ---------------------------------------------------------------------------- |
| 1. Nhận khiếu nại   | CSKH tiếp nhận cuộc gọi/chat từ KH hoặc TX                                                    | 2 phút    | SĐT, mã chuyến         | Ticket trên CRM      | Ít lỗi, nhanh                                                                |
| 2. Tra cứu GPS      | Mở Dashboard nội bộ, tìm chuyến theo mã, xem lộ trình thực tế                                 | 3 phút    | Mã chuyến              | Tọa độ, lộ trình, km | Phải mở nhiều tab                                                            |
| 3. Đối chiếu giá 🔴 | Mở file Excel bảng giá, tính cước theo km + thời gian chờ + phụ phí, so sánh với cước thực tế | 5 phút    | Km, thời gian, phụ phí | Số tiền chênh lệch   | **Bottleneck:** Dễ tính sai, bảng giá thay đổi theo mùa/khu vực              |
| 4. Soạn phản hồi 🔴 | Viết tin nhắn giải thích chi tiết bằng tiếng Việt thân thiện, dẫn số liệu cụ thể              | 5 phút    | Kết quả đối chiếu      | Tin nhắn phản hồi    | **Bottleneck:** Mỗi CSKH viết một kiểu, thiếu nhất quán, dễ quên dẫn số liệu |
| 5. Hoàn tiền        | Nếu khiếu nại hợp lệ, tạo lệnh hoàn tiền trên CRM                                             | 2 phút    | Quyết định duyệt       | Lệnh hoàn tiền       | Cần supervisor duyệt nếu > 100k                                              |

---

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field                       | Nội dung                                                                                                                                                                                                                                                                                                                                                                                                                     |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1. Actor / Operator**     | Nhân viên CSKH (Customer Service Representative) tại tổng đài Xanh SM, xử lý trung bình 15-20 khiếu nại cước mỗi ca.                                                                                                                                                                                                                                                                                                         |
| **2. Current Workflow**     | Khiếu nại đến qua hotline hoặc App → CSKH tra cứu lộ trình GPS chuyến đi trên Dashboard nội bộ → Mở file Excel bảng giá cước để tính toán chênh lệch theo km + thời gian chờ + phụ phí khu vực → Soạn tin nhắn phản hồi giải thích bằng tiếng Việt → Nếu hợp lệ, tạo lệnh hoàn tiền trên CRM. 5 bước, hoàn toàn thủ công, sử dụng 3 hệ thống rời rạc (GPS Dashboard, Excel, CRM).                                            |
| **3. Bottleneck**           | Bước 3 & 4 (chiếm 10/15 phút): Đối chiếu bảng giá thủ công dễ tính sai do bảng giá thay đổi theo mùa/khu vực/promotion; soạn phản hồi thiếu nhất quán giữa các CSKH, hay quên dẫn số liệu minh chứng.                                                                                                                                                                                                                        |
| **4. Business Impact**      | Mỗi ngày có ~200 khiếu nại cước tại Hà Nội + TP.HCM. Gây lãng phí ~50 giờ làm việc/ngày của team CSKH. Thời gian chờ phản hồi trung bình 45 phút khiến 30% khách hàng gửi thêm khiếu nại lần 2 hoặc đánh giá 1 sao trên App Store. Tỷ lệ CSKH tính sai chênh lệch cước ~12%, dẫn đến hoàn tiền sai hoặc thiếu, gây mất uy tín thương hiệu.                                                                                   |
| **5. Success Metric**       | 1. Giảm thời gian xử lý khiếu nại từ 15 phút/vụ xuống dưới 3 phút (Efficiency).<br>2. Tỷ lệ phản hồi đúng bảng giá đạt ≥ 95% (Quality).<br>3. Giảm tỷ lệ khiếu nại lần 2 từ 30% xuống dưới 10% (Customer Satisfaction).                                                                                                                                                                                                      |
| **6. Operational Boundary** | AI được phép: (a) tự động pull lộ trình GPS và tính toán chênh lệch cước, (b) soạn draft phản hồi giải thích chi tiết bằng tiếng Việt với số liệu minh chứng. **CẤM:** AI không được tự động gửi phản hồi cho khách hàng (bắt buộc CSKH review trước khi gửi — HITL); không được tự động tạo lệnh hoàn tiền (chỉ gợi ý số tiền, CSKH xác nhận); không được thay đổi bảng giá hoặc áp dụng promotion không có trong hệ thống. |

---

## 3.3. Future-State Flow & AI Fit

### AI Fit Matrix

| Giải pháp                | Có phù hợp?         | Lý do                                                                                                                                                                                                  |
| ------------------------ | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Rule / State-Machine** | ❌ Không phù hợp    | Phản hồi khiếu nại cần ngôn ngữ tự nhiên, linh hoạt theo từng trường hợp cụ thể — rule cứng không xử lý được sự đa dạng của tiếng Việt đời thường                                                      |
| **LLM Feature**          | ✅ **Phù hợp nhất** | Quy trình có cấu trúc rõ ràng (GPS → tính toán → soạn draft), đầu vào có dữ liệu có cấu trúc, đầu ra là text tiếng Việt — đúng thế mạnh của LLM. Rủi ro kiểm soát được vì chỉ soạn draft, không tự gửi |
| **Agentic Loop**         | ❌ Không cần thiết  | Không cần Agent tự ra quyết định hay duyệt multi-step — quy trình tuyến tính, không cần planning hay tool-use phức tạp                                                                                 |

**Lựa chọn:** LLM Feature (Gemini 2.5 Flash)

### Giải thích chi tiết các bước:

| Bước                    | Loại       | Mô tả                                                                                                           | Ranh giới                                                                  |
| ----------------------- | ---------- | --------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| Bước 2: Auto-pull GPS   | 🔵 AI Step | Hệ thống tự động pull lộ trình GPS từ mã chuyến, xác định điểm đón/trả, tổng km, thời gian chờ                  | Chỉ đọc, không sửa dữ liệu GPS                                             |
| Bước 3: Tính chênh lệch | 🔵 AI Step | LLM tính toán cước theo bảng giá hiện tại (km × đơn giá + thời gian chờ × phụ phí), so sánh với cước thực tế    | Chỉ dùng bảng giá có trong hệ thống, không áp dụng promotion tự tạo        |
| Bước 4: Soạn draft      | 🔵 AI Step | LLM soạn tin nhắn phản hồi tiếng Việt, dẫn số liệu cụ thể (km, thời gian, đơn giá, chênh lệch), tone thân thiện | **Bắt buộc** gắn `[DRAFT_ONLY]` ở đầu; chỉ soạn draft, không gửi trực tiếp |
| Bước 5: Review & Gửi    | 🟢 HITL    | CSKH đọc draft, chỉnh sửa nếu cần, xác nhận gửi cho khách hàng                                                  | CSKH có quyền reject toàn bộ draft và viết lại thủ công                    |

### Fallback Plan:

- **Nếu LLM trả về lỗi/timeout:** CSKH chuyển sang quy trình thủ công cũ (Bước 2-4 thủ công như hiện tại).
- **Nếu AI tính chênh lệch cước = 0 (không có lỗi hệ thống):** Hiển thị thông báo "Không phát hiện chênh lệch" để CSKH xác minh lại với khách hàng.
- **Nếu CSKH reject draft:** Ghi nhận lý do reject để cải thiện prompt cho lần sau (feedback loop).

---

# 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist

| #   | Tiêu chí                                                               | Trạng thái     | Ghi chú                                                                                                                                             |
| --- | ---------------------------------------------------------------------- | -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?                        | ✅ Có          | Dữ liệu lộ trình GPS, bảng giá cước, và lịch sử khiếu nại đã có sẵn trên hệ thống nội bộ Xanh SM. Có thể truy xuất qua API nội bộ.                  |
| 2   | Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)? | ✅ Có          | AI chỉ soạn draft, không tự gửi. CSKH luôn review trước khi gửi cho khách hàng. Nếu AI lỗi, fallback về quy trình thủ công cũ ngay lập tức.         |
| 3   | Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?                  | ⚠️ Cần đào tạo | CSKH hiện tại đã quen với quy trình thủ công. Cần 1-2 tuần đào tạo làm quen với giao diện mới có draft AI. Ban lãnh đạo Xanh SM đã ủng hộ thí điểm. |

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:

**[x] GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.

[ ] NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline): Trì hoãn để chuẩn bị thêm.
[ ] NO-GO (Không khả thi / Rule-based tốt hơn): Hủy bỏ dự án AI này.

### Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):

**1. Bằng chứng kỹ thuật:**

- Bài toán có tính cấu trúc cao: đầu vào là dữ liệu có sẵn (GPS, bảng giá), đầu ra là text tiếng Việt — đúng thế mạnh của LLM Feature, không cần Agent phức tạp.
- Rủi ro thấp: AI chỉ soạn draft (không tự gửi, không tự hoàn tiền), có HITL ở bước cuối cùng, có fallback rõ ràng khi AI lỗi.
- Dữ liệu sẵn sàng: Hệ thống GPS Dashboard và bảng giá cước đã có sẵn trên hệ thống nội bộ, không cần thu thập thêm dữ liệu mới.

**2. Ước lượng chi phí triển khai (Scope hẹp — MVP):**

| Hạng mục                                   | Chi phí ước tính   | Ghi chú                                                |
| ------------------------------------------ | ------------------ | ------------------------------------------------------ |
| Phát triển MVP (2 kỹ sư × 4 tuần)          | ~80 triệu VNĐ      | Xây dựng API tích hợp GPS + tính cước + gọi Gemini API |
| Chi phí Gemini API (200 vụ/ngày × 30 ngày) | ~3 triệu VNĐ/tháng | Gemini 2.5 Flash, ~500 token/vụ                        |
| Đào tạo CSKH (50 nhân viên × 2 buổi)       | ~10 triệu VNĐ      | Chi phí 1 lần                                          |
| **Tổng MVP (tháng đầu)**                   | **~93 triệu VNĐ**  |                                                        |
| **Vận hành hàng tháng**                    | **~3 triệu VNĐ**   | Chỉ chi phí API                                        |

**3. Lợi ích kỳ vọng:**

- Tiết kiệm ~50 giờ/ngày × 22 ngày = **1.100 giờ/tháng** của team CSKH (tương đương ~55 triệu VNĐ/tháng chi phí nhân sự).
- Giảm tỷ lệ khiếu nại lần 2 từ 30% → 10% → cải thiện điểm đánh giá App Store, giảm tỷ lệ rời bỏ khách hàng.
- **ROI: Hoàn vốn trong vòng 2 tháng đầu triển khai.**

**4. Scope hẹp cho MVP:**

- Chỉ áp dụng cho khiếu nại cước tại Hà Nội (chiếm ~60% tổng khiếu nại).
- Chỉ xử lý khiếu nại "cước cao hơn kỳ vọng" (không bao gồm khiếu nại về thái độ tài xế, sự cố an toàn).
- Đánh giá kết quả sau 1 tháng trước khi mở rộng sang TP.HCM và các loại khiếu nại khác.
