# Problem Statement: Tối Ưu Xử Lý Khiếu Nại Cước Xanh SM

## 1. Problem Statement
* **Actor:** Nhân viên CSKH Xanh SM (nhóm tiếp nhận khiếu nại cước).
* **Current Workflow:** Khách gọi báo thu sai cước -> NV mở CRM xem log -> Bật map đối chiếu đường đi thực tế vs dự kiến -> Quyết định có hoàn tiền hay không.
* **Bottleneck:** Khâu check map thủ công mất thời gian nhất, trung bình tốn tầm 5 phút/case.
* **Business Impact:** Khách gọi đông lúc cao điểm dễ kẹt tổng đài, rớt SLA, tốn thêm chi phí nhân sự trực.
* **Success Metric:** AI tự tóm tắt và đánh giá lệch tuyến chuẩn > 90%, ép thời gian xử lý xuống dưới 1 phút.
* **Operational Boundary:** AI chỉ phân tích và suggest mức đền bù; tuyệt đối không cấp quyền gọi API hoàn tiền trực tiếp vào ví khách (để chặn rủi ro tài chính).

## 2. RACI-Lite
* **Chốt bài toán & chỉ số đo lường:** Quản lý dự án là người phê duyệt và chịu trách nhiệm cuối cùng; Lập trình viên AI cùng Quản lý dự án là những người trực tiếp tính toán và thực hiện.
* **Thiết lập ranh giới an toàn (Boundary):** Giám đốc Kỹ thuật là người phê duyệt chốt; Trưởng nhóm Kỹ thuật là người trực tiếp thiết lập (code/cấu hình); Bộ phận Pháp chế và Bảo mật đóng vai trò cố vấn chuyên môn trước khi duyệt.
* **Quyết định vận hành (Go-live) & Trực hệ thống:** Trưởng nhóm Kỹ thuật hoặc Giám đốc Kỹ thuật là người ra quyết định và gánh trách nhiệm giải trình nếu AI gây lỗi; Lập trình viên trực hệ thống là người trực tiếp thao tác deploy và sửa lỗi nóng.

## 3. Readiness Scorecard (Đạt 5/5 YES)
* **Value:** Có (thiệt hại SLA và cost rõ ràng).
* **Baseline:** Có (5 phút/call thủ công).
* **Eval:** Có (dùng log CRM cũ để test).
* **Tolerance:** Chấp nhận được (vì AI chỉ suggest, sai số < 5%, người duyệt cuối).
* **Operations:** Rõ người chịu trách nhiệm (Trưởng nhóm Kỹ thuật).

## 4. Feasibility Check
* **Kỹ thuật:** Ổn, vì đã có baseline và data test sạch.
* **Vận hành:** Đã kẹp luồng human-in-the-loop duyệt lệnh.
* **Kinh doanh:** ROI dự kiến dương vì giảm được giờ làm của team CSKH.

## 5. Go/No-Go Decision
* **Quyết định:** **GO**.
* **Lý do:** Bài toán clear, đã cô lập được rủi ro bằng cách chỉ dùng LLM feature để tư vấn chứ không cấp quyền tự trị. Pass toàn bộ checklist khả thi.