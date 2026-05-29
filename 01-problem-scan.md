# Phase 1 — SCAN (Cá nhân)
---
## 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | Xanh SM | Lặp lại (Repetitive) | Điều phối viên phải liên tục điều chỉnh lộ trình và phân bổ xe khi có nhiều yêu cầu đặt xe đồng thời, lặp đi lặp lại mỗi giờ. |
| 2 | Vinhomes | Tốn thời gian (Time-consuming) | Nhân viên CSKH Vinhomes phải đọc và phản hồi hàng trăm đánh giá 1-2 sao của cư dân mỗi ngày, mất nhiều thời gian để soạn phản hồi phù hợp. |
| 3 | Vinmec | Pain từ người khác (Stakeholder Pain) | Bác sĩ mất quá nhiều thời gian viết tóm tắt hồ sơ bệnh án/xuất viện (mất 20-30 phút/bệnh nhân, bác sĩ phàn nàn vì quá tải). |
| 4 | VinFast | Pain từ người khác (Stakeholder Pain) | Chủ xe VinFast phàn nàn về thời gian chờ đợi quá dài tại trạm sạc do không có hệ thống dự đoán và đề xuất trạm sạc rảnh. |
| 5 | Vinpearl | Lặp lại (Repetitive) | Nhân viên lễ tân Vinpearl phải xác nhận lại thông tin đặt phòng, yêu cầu đặc biệt và hướng dẫn khách hàng check-in thủ công nhiều lần mỗi ngày. |

---
# Phase 2 — QUICK-ASSESS (Cá nhân)
---
## QUICK PROBLEM CARD #1
**Bài toán (1 câu):** Giảm thời gian phản hồi đánh giá tiêu cực của cư dân Vinhomes bằng AI.
**Công ty thành viên:** [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  [ ] Vinmec   [ ] Khác (Ghi rõ)________
**Ai đang đau (Actor)?** Nhân viên CSKH Vinhomes, cư dân Vinhomes
**Workflow thủ công hiện tại (3-5 bước):**
1. Nhân viên mở hệ thống quản lý đánh giá ──> 2. Đọc từng đánh giá 1-2 sao ──> 3. Soạn phản hồi phù hợp với tình hình ──> 4. Gửi phản hồi và ghi lại hành động
**Bước nào tốn thời gian/lỗi nhất?** Soạn phản hồi phù hợp (⏱ 8-10 phút/lượt)
**AI có thể nhảy vào hỗ trợ ở bước nào?** Đọc đánh giá, tạo bản nháp phản hồi phù hợp với chính sách Vinhomes
**Đo thành công bằng gì (Metric có số)?** Giảm thời gian soạn phản hồi từ 10 min ──> under 2 min, tăng số lượng phản hồi xử lý mỗi ngày từ 50 lên 200
**Quick Architecture:** [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent

---
## QUICK PROBLEM CARD #2
**Bài toán (1 câu):** Đề xuất trạm sạc rảnh và lộ trình tối ưu cho chủ xe VinFast để giảm thời gian chờ.
**Công ty thành viên:** [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  [ ] Vinmec   [ ] Khác (Ghi rõ)________
**Ai đang đau (Actor)?** Chủ xe VinFast, nhân viên hỗ trợ khách hàng VinFast
**Workflow thủ công hiện tại (3-5 bước):**
1. Chủ xe mở app VinFast ──> 2. Xem danh sách trạm sạc ──> 3. Đi đến trạm và chờ đợi nếu đông ──> 4. Gọi hotline nếu cần hỗ trợ
**Bước nào tốn thời gian/lỗi nhất?** Đi đến trạm và chờ đợi (⏱ 30-60 phút/lượt)
**AI có thể nhảy vào hỗ trợ ở bước nào?** Dự đoán tình trạng trạm sạc, đề xuất trạm rảnh và lộ trình tối ưu
**Đo thành công bằng gì (Metric có số)?** Giảm thời gian chờ trung bình từ 45 phút xuống dưới 10 phút, tăng tỷ lệ sử dụng trạm sạc đều đặn hơn 30%
**Quick Architecture:** [ ] No AI  [ ] Rule  [x] LLM  [x] Agent

---
## QUICK PROBLEM CARD #3
**Bài toán (1 câu):** Hỗ trợ bác sĩ Vinmec viết tổng hợp và kết luận thông tin bệnh án (Tóm tắt xuất viện - Discharge Summary).
**Công ty thành viên:** [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  [x] Vinmec   [ ] Khác (Ghi rõ)________
**Ai đang đau (Actor)?** Bác sĩ điều trị trực tiếp, bệnh nhân xuất viện tại Vinmec
**Workflow thủ công hiện tại (3-5 bước):**
1. Bác sĩ mở EMR bệnh án điện tử ──> 2. Đọc toàn bộ kết quả xét nghiệm, chẩn đoán, phác đồ điều trị ──> 3. Tự viết tay tóm tắt diễn biến lâm sàng & hướng dẫn ra viện ──> 4. Ký số & in tóm tắt giao cho khách hàng
**Bước nào tốn thời gian/lỗi nhất?** Soạn thảo và viết tay tóm tắt y khoa (⏱ 10-15 phút/lượt)
**AI có thể nhảy vào hỗ trợ ở bước nào?** Tự động đọc và tổng hợp dữ liệu lâm sàng đợt điều trị, tạo bản nháp tóm tắt xuất viện y khoa chuẩn Bộ Y tế.
**Đo thành công bằng gì (Metric có số)?** Giảm thời gian soạn tóm tắt từ 15 phút ──> dưới 3 phút/bệnh nhân, tăng số hồ sơ xử lý từ 15 lên 60/ngày.
**Quick Architecture:** [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent
