# 📘 Báo cáo Nhóm (Group Report) — Lab 02: AI Product Scoping

## Đơn vị: Vin Smart Future (Vingroup)

### Dự án: Trợ lý AI hỗ trợ soạn thảo Tóm tắt xuất viện Vinmec (Vinmec Discharge Summary Copilot)

***

### 👥 Khai báo Thành viên & Nhóm:

> **Nhóm:** Team Fast
> **Thành viên:**
>
> - [Nguyễn Minh Chiến] — [2A202600664]
> - [Vũ Tuấn Phương] — [2A202600772]
> - [Lê Quang Miền] — [2A202600715]
> - [Nguyễn Hải Quân] - [2A202600660]

***

# 🗳️ Quyết định Lựa chọn Bài toán của Nhóm

Nhóm **Team Fast** đã cùng thảo luận và thống nhất lựa chọn bài toán:
👉 **Ứng dụng AI hỗ trợ bác sĩ Vinmec viết tổng hợp và kết luận thông tin bệnh án (Tóm tắt xuất viện - Discharge Summary) từ hồ sơ bệnh án điện tử (EMR).**

### Lý do lựa chọn bài toán này:

1. **Tác động giảm tải y tế cực kỳ cao (High Business & Human Impact):** Các bác sĩ lâm sàng tại Vinmec đang bị quá tải nghiêm trọng bởi công việc hành chính y tế. Viết tóm tắt xuất viện thủ công từ đống hồ sơ bệnh án điện tử đồ sộ (kết quả xét nghiệm sinh hóa, chẩn đoán hình ảnh X-quang/MRI, nhật ký điều trị, đơn thuốc) mất từ 15-20 phút cho mỗi bệnh nhân. AI có thể giúp thu thập, phân tích và tổng hợp dữ liệu này trong vòng vài giây, giải phóng 80% thời gian giấy tờ để bác sĩ tập trung thăm khám lâm sàng cho bệnh nhân.
2. **Nâng cao trải nghiệm khách hàng (Patient Experience):** Giảm thiểu thời gian chờ đợi làm thủ tục xuất viện của bệnh nhân và người nhà tại Vinmec (SLA từ 3 tiếng xuống dưới 1 tiếng), nâng cao chỉ số hài lòng dịch vụ y tế đẳng cấp quốc tế của Vinmec.
3. **Tính khả thi cao về mặt công nghệ (High AI Fit):** Các dữ liệu lâm sàng trong hệ thống EMR đều có cấu trúc và ở dạng văn bản/chỉ số số học rõ ràng. LLM (như Gemini 2.5 Flash) có khả năng đọc hiểu, tóm tắt và đối chiếu logic dữ liệu y khoa cực kỳ chính xác để sinh ra các báo cáo y tế chuẩn hóa theo biểu mẫu quy định của Bộ Y tế.
4. **Rủi ro kiểm soát được bằng HITL:** Bác sĩ điều trị luôn là người chịu trách nhiệm chuyên môn cuối cùng. AI chỉ đóng vai trò trợ lý soạn nháp, bắt buộc bác sĩ phải kiểm duyệt kỹ lưỡng và ký số phê duyệt trên hệ thống EMR trước khi in ra, đảm bảo an toàn tuyệt đối về mặt pháp lý y khoa.

### Lý do loại bỏ các ý tưởng khác trong thẻ Quick Cards:

- **Card #1 (Vinhomes CSKH - Phản hồi đánh giá tiêu cực):** Mặc dù giúp ích cho CSKH nhưng không mang tính nhân văn và cấp bách cao bằng việc hỗ trợ giảm tải hành chính cho đội ngũ y bác sĩ đang trực tiếp cứu người tại Vinmec.
- **Card #2 (VinFast - Gợi ý lộ trình & trạm sạc):** Đòi hỏi hạ tầng dữ liệu thời gian thực và bản đồ giao thông phức tạp, cần nhiều hệ thống backend bên ngoài liên kết cùng lúc, chưa mang lại hiệu quả vận hành nội bộ tức thì.

***

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm)

## 3.1. Current-State Workflow (Quy trình vận hành hiện tại)

Quy trình xử lý thủ công thủ tục xuất viện y khoa của bác sĩ điều trị tại Vinmec:

```text
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ Bước 1          │     │ Bước 2          │     │ Bước 3          │     │ Bước 4          │
│ Mở hệ thống EMR │     │ Đọc & Thu thập  │     │ Tổng hợp & Phân │     │ Soạn thảo tóm   │
│ bệnh án điện tử │ ──→ │ hồ sơ đợt điều  │ ──→ │ tích chỉ số lâm │ ──→ │ tắt xuất viện   │
│                 │     │ trị bệnh nhân   │     │ sàng quan trọng │     │ y khoa chuẩn    │
│ Ai: Bác sĩ      │     │ Ai: Bác sĩ      │     │ Ai: Bác sĩ      │     │ Ai: Bác sĩ      │
│ ⏱ 1 phút        │     │ ⏱ 7-10 phút     │     │ ⏱ 5-7 phút      │     │ ⏱ 10-15 phút 🔴 │
│ In: Chỉ định xuất│     │ In: Kết quả XN, │     │ In: Dữ liệu thô │     │ In: Kết quả tổng│
│ Out: Hồ sơ bệnh │     │ hình ảnh, thuốc │     │ Out: Mối liên hệ│     │ Out: Draft giấy │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
                                                                                 │
                                                                                 ▼
                                                                        ┌─────────────────┐
                                                                        │ Bước 5          │
                                                                        │ Đối chiếu &     │
                                                                        │ kiểm duyệt      │
                                                                        │ pháp lý y tế    │
                                                                        │ Ai: Bác sĩ      │
                                                                        │ ⏱ 2 phút (🔄 HF)│
                                                                        │ Out: Bản duyệt  │
                                                                        └─────────────────┘
                                                                                 │
                                                                                 ▼
                                                                        ┌─────────────────┐
                                                                        │ Bước 6          │
                                                                        │ Ký số điện tử   │
                                                                        │ & In tóm tắt    │
                                                                        │ Ai: Bác sĩ      │
                                                                        │ ⏱ 1 phút        │
                                                                        │ Out: In giấy giao│
                                                                        └─────────────────┘
```

- 🔴 **Nút thắt cổ chai (Bottleneck):** **Bước 4 (Soạn thảo tóm tắt xuất viện)**. Bác sĩ mất rất nhiều thời gian để tự tổng hợp thông tin, viết tay tóm tắt diễn biến bệnh, lọc ra các chỉ số cận lâm sàng nổi bật và soạn phần dặn dò điều trị sau xuất viện bằng ngôn từ dễ hiểu cho bệnh nhân.
- 🔄 **Điểm chuyển giao thông tin (Handoff - HF):**
  - **Bước 5:** Đối chiếu chéo giữa bản viết tay tóm tắt y khoa với các quy định pháp lý của Bộ Y tế và tiêu chuẩn chất lượng JCI quốc tế của bệnh viện.
- ⏱ **Tổng thời gian xử lý thủ công:** **\~25 - 35 phút/bệnh nhân**.

***

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field                       | Nội dung chi tiết                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **1. Actor / Operator**     | Bác sĩ điều trị trực tiếp tại các khoa lâm sàng thuộc Bệnh viện Đa khoa Quốc tế Vinmec.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| **2. Current Workflow**     | Khi bệnh nhân có chỉ định xuất viện, bác sĩ phải mở hệ thống EMR, đọc lại toàn bộ hồ sơ bệnh án đồ sộ trong đợt điều trị (xét nghiệm máu, sinh hóa, X-quang, MRI, nhật ký điều trị, đơn thuốc), phân tích tổng hợp diễn biến bệnh và viết thủ công bản Tóm tắt xuất viện y khoa chuẩn Bộ Y tế, đối chiếu pháp lý rồi ký số in giao cho bệnh nhân.                                                                                                                                                                                                                                                                                                                                                                                    |
| **3. Bottleneck**           | **Bước 4 - Soạn thảo tóm tắt xuất viện:** Bác sĩ mất từ 10-15 phút để tự viết tay tóm tắt từ hàng chục tài liệu rời rạc, vừa phải đảm bảo tính chính xác lâm sàng, vừa phải diễn đạt dễ hiểu phần dặn dò cho bệnh nhân, và phải tuân thủ nghiêm ngặt quy định pháp lý của Bộ Y tế.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **4. Business Impact**      | Mỗi ngày, một bác sĩ xuất viện cho 10-15 bệnh nhân nội trú. Bác sĩ mất 4-5 tiếng/ngày chỉ để làm thủ tục giấy tờ hành chính. Điều này gây kiệt sức cho đội ngũ y khoa, làm giảm thời gian thăm khám lâm sàng trực tiếp cho bệnh nhân mới xuống dưới 10 phút, gia tăng tỷ lệ sai sót y tế ngoài ý muốn, và kéo dài thời gian làm thủ tục xuất viện của bệnh nhân (SLA xuất viện > 3 tiếng), làm giảm chỉ số CSI dịch vụ của Vinmec xuống dưới 80%.                                                                                                                                                                                                                                                                                    |
| **5. Success Metric**       | 1. **Thời gian soạn thảo:** Giảm thời gian bác sĩ viết tóm tắt xuất viện từ 15 phút xuống **dưới 3 phút/bệnh nhân** (Giảm 80%).2. **Hiệu suất thủ tục:** Tăng số lượng hồ sơ xuất viện được xử lý hoàn tất mỗi ngày từ **15 lên 60 hồ sơ/bác sĩ**.3. **Chất lượng y tế:** Đạt **100% độ chính xác lâm sàng**, tuyệt đối không xảy ra lỗi ảo tưởng (hallucination) nhờ cơ chế kiểm tra đối chiếu dữ liệu gốc.4. **Trải nghiệm khách hàng:** Giảm thời gian chờ đợi thanh toán và xuất viện của bệnh nhân xuống **dưới 45 phút** (đạt tiêu chuẩn JCI).                                                                                                                                                                                 |
| **6. Operational Boundary** | **CẤM (Do NOT):** AI tuyệt đối không được tự ý ký số hoặc tự động lưu bản tóm tắt vào EMR mà không qua bước kiểm duyệt thủ công của bác sĩ điều trị (Mọi bản nháp do AI soạn phải bắt đầu bằng nhãn `[DRAFT_ONLY]`).**CẤM (Do NOT):** AI tuyệt đối không được bịa đặt (hallucinate) hoặc tự ý chỉnh sửa kết quả xét nghiệm, chỉ số sinh tồn của bệnh nhân. Nếu dữ liệu cận lâm sàng mâu thuẫn hoặc thiếu chỉ số sinh tồn cốt lõi (huyết áp, nhịp tim), AI phải từ chối viết tóm tắt và đưa ra cảnh báo: `{"action": "manual_review_required", "reason": "<lý do mâu thuẫn dữ liệu>"}`.**CẤM (Do NOT):** AI không được tự ý đưa ra chẩn đoán y khoa mới, chỉ định xét nghiệm mới hoặc kê đơn thuốc mới ngoài hồ sơ gốc của bệnh nhân. |

***

## 3.3. Future-State Flow & AI Fit

- **Xác định mức AI Fit (AI-Fit Matrix):** Chọn **LLM Feature**. Giải pháp là một tính năng trợ lý thông minh (Copilot Feature) được tích hợp trong hệ thống EMR hiện tại của Vinmec để hỗ trợ bác sĩ soạn thảo bản nháp. Quy trình y khoa đòi hỏi tính nhất quán, an toàn tuyệt đối và sự kiểm soát chuyên môn tối cao của bác sĩ, do đó mô hình LLM Feature kết hợp Human-in-the-loop là hoàn hảo nhất, không sử dụng Agent tự trị.
- **Quy trình tương lai (Future-State Flow):**

```text
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ Bước 1          │     │ Bước 2          │     │ Bước 3          │     │ Bước 4          │
│ Nhận chỉ định   │ ──→ │ 🔵 AI Tự động   │ ──→ │ 🔵 AI soạn      │ ──→ │ 🟢 Bác sĩ       │
│ xuất viện mới   │     │ trích xuất loại │     │ bản nháp tóm    │     │ kiểm duyệt nháp │
│ trên hệ thống   │     │ hồ sơ lâm sàng  │     │ tắt [DRAFT_ONLY]│     │ chỉnh sửa & ký  │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
                                                                                 │
                                                                                 ▼
                                                                        ↩️ Fallback:
                                                                        Nếu AI phát hiện
                                                                        XN mâu thuẫn hoặc
                                                                        sinh lỗi, hệ thống
                                                                        tắt AI, chuyển
                                                                        bác sĩ tự viết tay.
```

***

# 🏁 Phase 5 — EVALUATE (Nhóm)

### AI Readiness Checklist:

1. [x] **Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?**
   - *Chứng cứ:* Có. Vinmec sở hữu hệ thống dữ liệu bệnh án điện tử đồ sộ đã được mã hóa ẩn danh để làm tập dữ liệu mẫu chuẩn hóa, huấn luyện và kiểm thử ranh giới mô hình.
2. [x] **Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?**
   - *Chứng cứ:* Có. Bác sĩ điều trị trực tiếp kiểm duyệt và ký số là chốt chặn pháp lý tối cao cuối cùng, đảm bảo an toàn tuyệt đối cho bệnh nhân trước khi in văn bản y khoa ra ngoài.
3. [x] **Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?**
   - *Chứng cứ:* Ban Giám đốc Vinmec và các Trưởng khoa lâm sàng cực kỳ ủng hộ dự án vì họ đang phải chịu sức ép lớn về chỉ tiêu SLA xuất viện và tình trạng kiệt sức hành chính của bác sĩ.

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:

👉 **GO (Bắt đầu xây dựng Prototype)**

### Lập luận kỹ thuật và ước lượng chi phí chặt chẽ:

1. **Tính khả thi kỹ thuật:** Gemini 2.5 Flash xử lý các thuật ngữ chuyên ngành y tế cực kỳ tốt, hỗ trợ tiếng Việt y khoa trôi chảy và có khả năng phát hiện logic mâu thuẫn dữ liệu rất cao khi được cung cấp System Prompt nghiêm ngặt.
2. **Tính kinh tế (ROI cao, Chi phí thấp):**
   - *Chi phí API:* Gemini 2.5 Flash có giá chỉ $0.075 / 1 triệu tokens input. Mỗi đợt điều trị của bệnh nhân trung bình khoảng 3,000 tokens đầu vào = $0.000225 (\~5.5 VND) cho một ca tóm tắt.
   - *Chi phí hàng ngày/khoa:* 20 ca xuất viện/ngày × 5.5 VND = \~110 VND/ngày/khoa. Một chi phí siêu rẻ so với giá trị tiết kiệm được.
   - *Giá trị tiết kiệm:* Tiết kiệm 4 giờ làm việc hành chính/ngày của từng bác sĩ chuyên môn cao, tương đương với việc tăng 30% năng suất thăm khám lâm sàng trực tiếp của bệnh viện, giúp bệnh viện Vinmec tối ưu hóa doanh thu khám bệnh đáng kể mà không cần tuyển thêm nhân sự hành chính phụ trợ.

***

*Báo cáo được thống nhất và phê duyệt bởi toàn thể thành viên nhóm Team Fast.*
