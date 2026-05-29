# Phase 3 — DEEP-DIVE & Phase 5 — EVALUATE (Báo cáo nhóm)

---

## 👥 0. Khai báo thành viên Nhóm

*   **Tên nhóm:** **TeamFast**
*   **Thành viên tham gia dự án:**
    1.  **Nguyễn Hải Quân** (MSSV: 2A202600660)
    2.  **Vũ Tuấn Phương** (MSSV: 2A202600772)
    3.  **Nguyễn Minh Chiến** (MSSV: 2A202600664)
    4.  **Lê Quang Miền** (MSSV: 2A202600715)

---

## 🗳️ 1. Quyết định lựa chọn Bài toán của nhóm

Nhóm **TeamFast** thống nhất lựa chọn bài toán: **"Tối Ưu Xử Lý Khiếu Nại Cước Xanh SM"** để tiến hành phân tích sâu (Deep-Dive).

### 💡 Lý do lựa chọn và loại bỏ các thẻ khác:
*   **Vì sao chọn Bài toán Khiếu Nại Cước Xanh SM?**
    *   **Tác động kinh doanh tức thì (Direct Business Impact):** Khách hàng gọi báo thu sai cước tăng cao vào giờ cao điểm, gây tắc nghẽn tổng đài nghiêm trọng, rớt SLA và tăng cao chi phí nhân lực CSKH. AI có thể giúp giải phóng sức lao động ở khâu tốn thời gian nhất.
    *   **Baseline và Dữ liệu rõ ràng:** Đã có log CRM lịch sử cụ thể, dễ dàng đối chiếu lộ trình thực tế so với lộ trình dự kiến để làm dữ liệu kiểm thử (Evaluation dataset).
    *   **Mức độ rủi ro kiểm soát được:** AI chỉ đóng vai trò phân tích dữ liệu tuyến đường, tóm tắt sự kiện và gợi ý mức đền bù (Suggest). Việc thực thi lệnh hoàn tiền bắt buộc phải thông qua sự kiểm duyệt của nhân viên CSKH (Human-in-the-loop), chặn hoàn toàn rủi ro tài chính trực tiếp.
*   **Vì sao loại bỏ các thẻ khác?**
    *   *Card #2 (Vinhomes CSKH)*: Yêu cầu kết nối quá nhiều phòng ban và các phân khu căn hộ phức tạp, rủi ro tranh chấp pháp lý cao, chưa có baseline cụ thể về thời gian phản hồi thực tế.
    *   *Card #3 (Vinmec Discharge Summary)*: Mức độ chấp nhận sai sót (Error Tolerance) cực kỳ thấp do liên quan trực tiếp đến sức khỏe và tính mạng bệnh nhân. AI chưa sẵn sàng cho việc tự động tóm tắt lâm sàng khi chưa được huấn luyện chuyên biệt.

---

## 🏗️ 2. Phase 3 — DEEP-DIVE: Phân Tích Sâu Bài Toán

### 3.1. Sơ đồ quy trình hiện tại (Current-State Workflow Diagram)
*Chi tiết sơ đồ được mô hình hóa trực quan tại tệp đính kèm [04-workflow-diagram.png](04-workflow-diagram.png)*

Tóm tắt các bước thủ công hiện tại của Điều phối viên / Nhân viên CSKH:
1.  **Bước 1: Tiếp nhận cuộc gọi khiếu nại (⏱ 1 phút)** — Khách hàng gọi điện lên tổng đài báo xe đi sai đường hoặc thu cước sai so với app.
2.  **Bước 2: Mở CRM xem log (⏱ 1 phút)** — Nhân viên mở CRM, tra cứu mã chuyến đi, biển số xe và xem lịch sử trạng thái.
3.  **Bước 3: Check map đối chiếu lộ trình (⏱ 5 phút - 🔴 Bottleneck)** — Nhân viên mở Google Maps/Bản đồ nội bộ, nhập tọa độ GPS thực tế của chuyến đi và đối chiếu thủ công với lộ trình dự kiến ban đầu trên app để xác định xem tài xế có đi lệch tuyến hay không.
4.  **Bước 4: Quyết định đền bù hoàn tiền (⏱ 2 phút)** — Nhân viên tính toán số tiền chênh lệch dựa trên số km lệch tuyến và đưa ra quyết định đền bù.
5.  **Bước 5: Thao tác làm lệnh hoàn tiền (⏱ 1 phút)** — Nhân viên nhập lệnh hoàn tiền thủ công vào ví của khách trên CRM.

⏱ **Tổng thời gian xử lý thủ công trung bình:** **10 phút/case** (riêng khâu đối chiếu map mất 5 phút).

---

### 3.2. Problem Statement (6-field) — Tiêu chuẩn Vin Smart Future

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH Xanh SM (nhóm tiếp nhận khiếu nại cước). |
| **2. Current Workflow** | Khách gọi báo thu sai cước -> Nhân viên mở CRM xem log chuyến đi -> Bật bản đồ đối chiếu đường đi thực tế vs dự kiến -> Tính toán số tiền lệch -> Quyết định có hoàn tiền hay không. Quy trình thủ công hoàn toàn, tốn 10 phút xử lý/case. |
| **3. Bottleneck** | Bước 3 (mất 5 phút): Khâu check map thủ công, đối chiếu tọa độ GPS thực tế của chuyến xe với lộ trình khuyến nghị trên app để phát hiện điểm lệch tuyến. |
| **4. Business Impact** | Vào giờ cao điểm, lượng cuộc gọi khiếu nại cước dồn ứ lớn gây nghẽn mạng tổng đài, rớt chỉ số cam kết chất lượng dịch vụ (SLA), gây stress cho nhân viên và tốn thêm chi phí thuê nhân sự trực ca đêm. |
| **5. Success Metric** | 1. AI tự động tóm tắt chuyến đi và đánh giá lệch tuyến chuẩn xác **> 90%**.<br>2. Ép tổng thời gian xử lý từ 10 phút xuống dưới **1 phút** (giảm 90% thời gian chờ đợi của khách). |
| **6. Operational Boundary** | **RANH GIỚI AN TOÀN BẮT BUỘC:**<br>1. AI chỉ được phép phân tích hành trình, tóm tắt và **soạn thảo đề xuất (suggest)** mức đền bù ở dạng nháp (Draft).<br>2. **CẤM TUYỆT ĐỐI** cấp quyền cho AI tự động gọi API hoàn tiền trực tiếp vào ví của khách hàng mà không có sự phê duyệt của nhân viên CSKH (Chốt chặn Human-in-the-loop để chặn rủi ro thất thoát tài chính). |

---

### 3.3. Future-State Flow & AI Fit

*   **Phân loại AI Fit:** Chọn giải pháp **LLM Feature** (quy trình nghiệp vụ có cấu trúc cố định, dữ liệu dạng văn bản và tọa độ rõ ràng, chỉ cần tính năng AI hỗ trợ đắc lực tích hợp vào CRM hiện tại).
*   **Sơ đồ tương lai (Future-State Workflow):**

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1:         │       │ Bước 2:         │       │ Bước 3:         │
│ Khách gọi báo   │ ────> │ CRM tự động     │ ────> │ 🔵 AI phân tích │
│ thu sai cước    │       │ trích xuất GPS  │       │ lộ trình, tự    │
│                 │       │ thực tế & dự kiến│      │ soạn nháp đề xuất│
└─────────────────┘       └─────────────────┘       └─────────────────┘
                                                             │
                                                             ▼
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Kết thúc:       │       │ Bước 4:         │       │ [Quyết định]    │
│ Khách nhận tiền │ <──── │ 🟢 CSKH click   │ <──── │ AI kiểm tra     │
│ hoàn (nếu có)   │       │ duyệt & hệ thống│       │ ranh giới:      │
│                 │       │ tự gọi API hoàn │       │ Chỉ Suggest?    │
└─────────────────┘       └─────────────────┘       └─────────────────┘
                                                             │
                                                  API Hoàn tiền tự động?
                                                             ▼
                                                    ↩️ Fallback/Alert:
                                                    Chặn lệnh! Bắt buộc
                                                    chuyển CSKH duyệt tay!
```

---

## 👥 3. Bản đồ Trách Nhiệm RACI-Lite

Để vận hành hệ thống AI này một cách an toàn và trơn tru tại Vin Smart Future, nhóm thiết lập mô hình phân định trách nhiệm RACI-Lite như sau:

*   **Chốt bài toán & chỉ số đo lường:**
    *   *Người phê duyệt & chịu trách nhiệm cuối cùng (Accountable):* Quản lý dự án (Project Manager).
    *   *Người trực tiếp thực hiện (Responsible):* Lập trình viên AI (AI Engineer) cùng Quản lý dự án.
*   **Thiết lập ranh giới an toàn (Operational Boundary):**
    *   *Người phê duyệt chốt (Accountable):* Giám đốc Kỹ thuật (CTO).
    *   *Người trực tiếp thiết lập code/cấu hình (Responsible):* Trưởng nhóm Kỹ thuật (Tech Lead).
    *   *Cố vấn chuyên môn (Consulted):* Bộ phận Pháp chế (Legal) và Bộ phận Bảo mật (Security).
*   **Quyết định vận hành (Go-live) & Trực hệ thống:**
    *   *Người ra quyết định & gánh trách nhiệm giải trình (Accountable):* Trưởng nhóm Kỹ thuật hoặc Giám đốc Kỹ thuật.
    *   *Người trực tiếp deploy và sửa lỗi nóng (Responsible):* Lập trình viên trực hệ thống (System Reliability Engineer).

---

## 🏁 4. Phase 5 — EVALUATE: Đánh Giá Khả Thi & Quyết Định

### 📊 Readiness Scorecard (Đạt 5/5 YES)
1.  **Value (Giá trị):** **YES** — Giải quyết trực tiếp thiệt hại về SLA tổng đài CSKH và tiết kiệm tối đa chi phí nhân lực vận hành.
2.  **Baseline (Điểm chuẩn):** **YES** — Đã đo lường rõ ràng baseline thủ công là 10 phút xử lý mỗi cuộc gọi (trong đó 5 phút check map).
3.  **Eval (Đánh giá):** **YES** — Có sẵn kho dữ liệu lịch sử log CRM chuyến đi sạch sẽ để làm tập kiểm thử.
4.  **Tolerance (Mức chấp nhận sai sót):** **YES** — Chấp nhận được vì AI chỉ đóng vai trò tư vấn, sai số dự kiến < 5%, nhân viên CSKH là người phê duyệt cuối cùng.
5.  **Operations (Vận hành):** **YES** — Phân định rõ ràng trách nhiệm vận hành và xử lý sự cố hệ thống (Trưởng nhóm Kỹ thuật chịu trách nhiệm chính).

---

### 🛠️ Đánh giá tính khả thi (Feasibility Check)
*   **Kỹ thuật:** Khả thi cực kỳ cao. Thuật toán so khớp bản đồ và phát hiện lệch tuyến đã có sẵn baseline kỹ thuật tốt, dữ liệu cấu trúc đầu vào sạch sẽ.
*   **Vận hành:** Cực kỳ an toàn do đã kẹp luồng human-in-the-loop phê duyệt mọi lệnh hoàn tiền, tránh nguy cơ thất thoát tiền của GSM.
*   **Kinh doanh:** ROI dự kiến đạt mức dương ngay trong tháng đầu tiên vận hành nhờ cắt giảm đáng kể số lượng nhân sự trực tổng đài và tăng độ hài lòng của khách hàng (SLA xử lý khiếu nại dưới 1 phút).

---

### 🚀 Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:

**[ x ] GO (Bắt đầu xây dựng Prototype)**

*   **Luận điểm lý giải quyết định (Justification):**
    Bài toán cực kỳ rõ ràng, giải quyết đúng điểm nghẽn vận hành tốn thời gian nhất của khối Xanh SM. Rủi ro của mô hình AI đã được cô lập hoàn hảo bằng cách chỉ dùng tính năng LLM Feature tư vấn soạn nháp chứ không cấp quyền tự trị gọi API ví khách hàng. Dự án vượt qua xuất sắc cả 5 tiêu chí của Readiness Scorecard và pass toàn bộ checklist khả thi kỹ thuật - vận hành - kinh doanh.
