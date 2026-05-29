# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🏛️ 1. Bối cảnh thực tế: Vin Smart Future (Vingroup)

**Vingroup** — Tập đoàn tư nhân lớn nhất Việt Nam — vừa sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành một đơn vị công nghệ thống nhất mang tên **Vin Smart Future**.

Nhiệm vụ của **Vin Smart Future** là xây dựng các giải pháp AI, số hóa, và tự động hóa cốt lõi để nâng cao hiệu suất vận hành và trải nghiệm khách hàng xuyên suốt các công ty thành viên:

- 🚗 **VinFast:** Hệ thống xe điện thông minh (EV), trợ lý AI ảo trong xe, dự đoán bảo trì pin, và quản lý chuỗi cung ứng sản xuất.
- 🚕 **Xanh SM (GSM):** Vận hành đội xe taxi/xe máy điện thông minh, điều vận thông minh (Smart Dispatching), tối ưu hóa lộ trình di chuyển.
- 🏢 **Vinhomes:** Quản lý đô thị thông minh (Smart Cities), trợ lý cư dân thông minh, tối ưu hóa mức tiêu thụ năng lượng.
- 🏥 **Vinmec:** Y tế thông minh, chẩn đoán hình ảnh bằng AI, tối ưu hóa quản lý hồ sơ bệnh án.
- 🎢 **Vinpearl / VinWonders:** Trải nghiệm du lịch số hóa, quản lý phòng và luồng khách thông minh tại các khu vui chơi.

Trong buổi Lab hôm nay, nhóm của bạn sẽ đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tiến hành tìm kiếm, scoping, phân tích độ khả thi, thiết lập ranh giới vận hành, và xây dựng một **bản mẫu kỹ thuật (prompt prototype)** cho một bài toán cụ thể thuộc một trong những mảng kinh doanh trên.

---

## 📊 2. Cơ cấu tính điểm bài lab

### 👥 Điểm nhóm (60 điểm)

| Gate                         | Điểm | Deliverable       | Tiêu chí chấm                                                                |
| ---------------------------- | ---: | ----------------- | ---------------------------------------------------------------------------- |
| **G1. Workflow Mapping**     |   20 | Problem Deep-Dive | Vẽ chi tiết quy trình hiện tại: các bước, handoff, thời gian, bottleneck     |
| **G2. Problem Statement**    |   20 | Problem Deep-Dive | Problem Statement 6-field bám sát thực tế, metric có số và ranh giới rõ ràng |
| **G3. AI Fit & Future Flow** |   10 | Problem Deep-Dive | So sánh Rule vs LLM vs Agent, future flow có bước AI, ranh giới và Fallback  |
| **G4. Decision Quality**     |   10 | Problem Deep-Dive | Quyết định Go/Not Yet/No-Go trung thực và có chứng cứ rõ ràng                |

### 👤 Điểm cá nhân (40 điểm)

| Gate                        | Điểm | Deliverable  | Tiêu chí chấm                                                                     |
| --------------------------- | ---: | ------------ | --------------------------------------------------------------------------------- |
| **I1. Scan & Cards**        |   15 | Quick Cards  | Liệt kê 5 problems sử dụng 3 lenses, hoàn thiện 3 quick cards chất lượng          |
| **I2. Prototyping**         |   10 | 02-lab/      | Chạy thử nghiệm programmatic prompt prototype thành công                          |
| **I3. AI Log & Reflection** |   15 | 03-ai-log.md | Phản ánh trung thực về việc dùng AI làm thought-partner (giúp gì, sai gì, sửa gì) |

---

# 🚀 Phase 0 — worked Example: Xanh SM Intelligent Dispatcher (15 min)

_Giảng viên walk-through ví dụ thực tế từ Vin Smart Future để bạn hiểu rõ cách scoping một bài toán AI._
Đọc chi tiết worked example tại file [02-deliverable-example.md](02-deliverable-example.md).

---

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:

1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

### 📝 List bài toán của tôi:

| #   | Subsidiary (VinFast/Xanh SM...) | Lens               | Mô tả ngắn bài toán                                                                                                                                                                                     |
| --- | ------------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **Vinhomes**                    | Lặp lại            | Phân loại và chuyển tuyến khiếu nại cư dân (mất nước, hỏng thang máy, ồn ào) gửi qua App Vinhomes Resident đến đúng ban quản lý tòa nhà — hiện tại nhân viên CSKH đọc từng ticket thủ công.             |
| 2   | **Xanh SM**                     | Tốn thời gian      | Xử lý khiếu nại cước phí của khách hàng và tài xế — nhân viên CSKH phải nghe ghi âm, đối chiếu lộ trình GPS, so sánh bảng giá, rồi soạn phản hồi thủ công, mất 15-20 phút/vụ.                           |
| 3   | **VinFast**                     | AI có thể tốt hơn  | Khách hàng mô tả lỗi xe bằng tiếng Việt đời thường (VD: "xe qua gờ giảm tốc kêu cụp cụp ở bánh trước") nhưng tổng đài phải tra cứu mã lỗi thủ công, phản hồi chậm 24-48h.                               |
| 4   | **Vinmec**                      | Tốn thời gian      | Bác sĩ soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary) thủ công từ bệnh án điện tử, xét nghiệm, ghi chú lâm sàng — mất 20-30 phút/bệnh nhân, bác sĩ phàn nàn vì quá tải.                          |
| 5   | **Vinpearl / VinWonders**       | Pain từ người khác | Quản lý khu vui chơi phải đọc hàng trăm review trên Booking, Agoda, Google Map mỗi tuần để lọc phản hồi tiêu cực ("phòng bẩn", "nhân viên thái độ tệ") — mất 8-10 giờ/tuần, bỏ lọt nhiều case khẩn cấp. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** dưới đây (10 phút/card).

---

## QUICK PROBLEM CARD #1

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán (1 câu): Hệ thống phân loại & chuyển tuyến tự     │
│ động khiếu nại cư dân Vinhomes đến đúng ban quản lý tòa.   │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH Vinhomes tại tổng đài   │
│ và ban quản lý từng tòa nhà.                                │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi khiếu nại qua App Vinhomes Resident         │
│   ──> 2. Nhân viên CSKH đọc nội dung, xác định loại sự cố   │
│   ──> 3. CSKH tìm đúng ban quản lý tòa nhà phụ trách       │
│   ──> 4. Chuyển ticket + soạn tóm tắt gửi ban quản lý       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 8 phút/ticket)│
│ Phân loại sai tuyến do đọc lướt, dẫn đến chuyển nhầm tòa.   │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (LLM đọc nội dung khiếu nại → phân loại loại sự cố →       │
│  tự động chuyển đúng ban quản lý tòa nhà)                   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? ______________________ │
│   "85% ticket được phân loại đúng tuyến trong dưới 10 giây, │
│    giảm thời gian xử lý trung bình từ 8 phút xuống 1 phút" │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

## QUICK PROBLEM CARD #2

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán (1 câu): Tối ưu xử lý khiếu nại cước phí Xanh SM  │
│ bằng cách tự động đối chiếu lộ trình, cước và soạn phản hồi│
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên CSKH Xanh SM tại tổng đài     │
│ xử lý khiếu nại cước; khách hàng & tài xế chờ phản hồi lâu. │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Khách hàng/tài xế gửi khiếu nại cước qua hotline/App   │
│   ──> 2. CSKH mở hệ thống, tra cứu lộ trình GPS chuyến đi  │
│   ──> 3. Đối chiếu bảng giá cước theo km + thời gian chờ   │
│   ──> 4. Soạn tin nhắn phản hồi giải thích cho khách hàng   │
│   ──> 5. Nếu khiếu nại hợp lệ, tạo lệnh hoàn tiền thủ công │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-4 (⏱ 15 phút/vụ)  │
│ CSKH phải mở nhiều hệ thống (GPS, bảng giá, CRM) rồi soạn  │
│ phản hồi thủ công, dễ tính sai hoặc phản hồi thiếu nhất quán│
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-4              │
│ (LLM tự động pull lộ trình GPS → tính toán chênh lệch cước  │
│  → soạn draft phản hồi giải thích chi tiết cho khách hàng)  │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? ______________________ │
│   "Giảm thời gian xử lý khiếu nại từ 15 phút/vụ xuống dưới │
│    3 phút; tỷ lệ phản hồi đúng bảng giá đạt 95%"           │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

## QUICK PROBLEM CARD #3

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán (1 câu): Trợ lý sơ bộ phân loại triệu chứng &     │
│ gợi ý chuyên khoa cho bệnh nhân đặt lịch khám Vinmec.      │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [x] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau (Actor)? Bệnh nhân đặt lịch khám phải chờ lâu  │
│ để được tư vấn chuyên khoa; lễ tân Vinmec xử lý thủ công.  │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bệnh nhân gọi hotline hoặc đến quầy mô tả triệu chứng │
│   ──> 2. Lễ tân ghi chép triệu chứng vào giấy/form         │
│   ──> 3. Lễ tân tra cứu bảng chuyên khoa, gợi ý bác sĩ     │
│   ──> 4. Xếp lịch hẹn + thông báo cho bệnh nhân            │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2-3 (⏱ 10 phút/khách│
│ Lễ tân không có chuyên môn y khoa, dễ gợi ý sai chuyên khoa │
│ dẫn đến bệnh nhân phải khám lại, tốn thời gian cả hai bên. │
│                                                             │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (Chatbot hỏi triệu chứng có cấu trúc → LLM phân tích →     │
│  gợi ý chuyên khoa phù hợp + mức độ ưu tiên khám)           │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? ______________________ │
│   "70% bệnh nhân được gợi ý đúng chuyên khoa ngay lần đầu; │
│    giảm thời gian tư vấn ban đầu từ 10 phút xuống 2 phút"  │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```
