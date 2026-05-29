# Phase 1 — SCAN & Phase 2 — QUICK-ASSESS (Bài cá nhân)

---

## 🔍 Phase 1 — SCAN: Bảng Quét Cơ Hội AI tại Vingroup

Dưới đây là bảng quét qua 5 bài toán và nút thắt cổ chai (bottleneck) thực tế trong vận hành tại các công ty thành viên thuộc Vingroup, sử dụng **4 Lenses** để đánh giá:

| # | Subsidiary | Lens | Mô tả ngắn bài toán & Bottleneck |
|---|------------|------|----------------------------------|
| 1 | **Xanh SM** | Tốn thời gian / Pain từ người khác | Nhân viên CSKH đối chiếu thủ công đường đi thực tế trên bản đồ so với lộ trình dự kiến khi khách báo khiếu nại cước (mất 5 phút/lượt). |
| 2 | **VinFast** | Lặp lại | So khớp dữ liệu sạc điện hằng tuần từ hàng nghìn trụ sạc liên kết ngoài của đối tác với hóa đơn thực tế gửi về hệ thống kế toán. |
| 3 | **Vinhomes** | AI-upgrade / Lặp lại | Phân loại tự động các phản ánh/khiếu nại của cư dân gửi qua App Vinhomes Resident để chuyển đến đúng Ban quản lý từng phân khu (hiện tại CSKH phản hồi thủ công rất chậm). |
| 4 | **Vinmec** | Tốn thời gian | Bác sĩ mất quá nhiều thời gian soạn thảo văn bản Tóm tắt hồ sơ xuất viện (Discharge Summary) từ các bệnh án điện tử và kết quả xét nghiệm (mất 20-30 phút/bệnh nhân). |
| 5 | **Xanh SM** | Stakeholder Pain | Điều phối viên xử lý thủ công các sự cố khẩn cấp từ tài xế báo hết pin giữa đường, tra cứu trạm sạc trống và soạn tin hướng dẫn (mất 12-15 phút/lượt). |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn **top 3 bài toán** tiềm năng nhất từ danh sách trên để phân tích sơ bộ:

### 🎫 QUICK PROBLEM CARD #1: Xanh SM Tối Ưu Xử Lý Khiếu Nại Cước

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Khách hàng gọi báo thu sai cước, CSKH phải check   │
│ và đối chiếu map thủ công lộ trình để quyết định hoàn tiền. │
│ Công ty thành viên: [x] Xanh SM (GSM)                       │
│                                                             │
│ Ai đang đau? Khách hàng (chờ lâu), Nhân viên CSKH (quá tải) │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Khách gọi tổng đài báo thu sai cước chuyến đi          │
│   ──> 2. Nhân viên mở hệ thống CRM để xem log chi tiết      │
│   ──> 3. Bật map đối chiếu lộ trình đi thực tế vs dự kiến   │
│   ──> 4. Quyết định hoàn tiền và làm lệnh chuyển tiền       │
│                                                             │
│ Bước nào tốn nhất? Bước 3 (Đối chiếu map: ⏱ 5 phút/case)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4            │
│ (AI tự tóm tắt và đánh giá lệch tuyến thực tế vs dự kiến)   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Ép thời gian xử lý khiếu nại từ 5 phút ──> dưới 1 phút;     │
│ Tỉ lệ AI tóm tắt và đánh giá lệch tuyến chuẩn xác > 90%.    │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Tự động soạn chỉ dẫn)   │
└─────────────────────────────────────────────────────────────┘
```

---

### 🎫 QUICK PROBLEM CARD #2: Vinhomes Phân loại & Điều hướng phản ánh cư dân

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Cư dân Vinhomes gửi phản ánh qua app bị dồn ứ,    │
│ cần phân loại và route tự động đến đúng ban quản lý.       │
│ Công ty thành viên: [x] Vinhomes                            │
│                                                             │
│ Ai đang đau? Cư dân (chờ lâu), Nhân viên CSKH (phân loại tay)│
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Cư dân gửi text/ảnh phản ánh lên App Vinhomes Resident │
│   ──> 2. Nhân viên CSKH trung tâm đọc từng tin nhắn để phân │
│          loại (Mất nước, Điện, An ninh, Vệ sinh, Đỗ xe...) │
│   ──> 3. Gửi ticket thủ công về Ban Quản Lý (BQL) toà nhà   │
│   ──> 4. BQL toà nhà tiếp nhận và xử lý                     │
│                                                             │
│ Bước nào tốn nhất? Bước 2 (⏱ 8 phút/phản ánh do lượng lớn)  │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2-3              │
│ (AI đọc mô tả phản ánh -> Tự phân loại và route ticket)     │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian điều hướng từ 12 giờ xuống dưới 10 phút;     │
│ Độ chính xác phân loại tự động đạt trên 92%.                │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Phân loại & Tagging)   │
└─────────────────────────────────────────────────────────────┘
```

---

### 🎫 QUICK PROBLEM CARD #3: Vinmec Hỗ trợ Soạn tóm tắt xuất viện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Bác sĩ tốn quá nhiều thời gian viết tay tóm tắt   │
│ bệnh án bằng ngôn ngữ dễ hiểu để bệnh nhân xuất viện.       │
│ Công ty thành viên: [x] Vinmec                              │
│                                                             │
│ Ai đang đau? Bác sĩ điều trị (quá tải hành chính)           │
│                                                             │
│ Workflow thủ công hiện tại (4 bước):                        │
│   1. Bác sĩ mở bệnh án điện tử EHR của bệnh nhân            │
│   ──> 2. Đọc lại lịch sử điều trị, xét nghiệm, đơn thuốc    │
│   ──> 3. Tự soạn bản Tóm tắt xuất viện bằng văn bản tiếng   │
│          Việt dễ hiểu cho bệnh nhân dễ theo dõi tại nhà     │
│   ──> 4. Ký và bàn giao bản cứng cho bệnh nhân              │
│                                                             │
│ Bước nào tốn nhất? Bước 3 (⏱ 15 phút/bệnh án)               │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3                │
│ (AI trích xuất thông tin EHR -> Tự động draft tóm tắt)      │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│ Giảm thời gian soạn tóm tắt từ 20 phút ──> dưới 3 phút/ca;  │
│ 100% hồ sơ draft được bác sĩ kiểm duyệt trước khi ký.        │
│                                                             │
│ Quick Architecture: [x] LLM Feature (Trích xuất & Tóm tắt)  │
└─────────────────────────────────────────────────────────────┘
```
