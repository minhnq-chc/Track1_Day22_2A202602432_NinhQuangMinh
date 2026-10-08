# Báo cáo Lab 22 — AI Product GTM & Monetization Model
## Giải pháp Trợ lý AI Tiếp nhận và Phân tầng Triệu chứng Y tế (Vinmec Clinical Triage)

- Học viên: Ninh Quang Minh
- Mã học viên: 2A202602432
- Lớp: AI Thực chiến Khóa IV — Track 1
- Sản phẩm: Trợ lý AI Tiếp nhận và Phân tầng Triệu chứng Lâm sàng (Clinical Triage & Patient Intake)
- Đối tác triển khai: Hệ thống Y tế Vinmec
- Ngày cập nhật dữ liệu giá: 08/10/2026

---

## 1. Tóm tắt Chỉ số Kinh tế Đơn vị (Unit Economics Summary)

Bảng tổng hợp các chỉ số tài chính và vận hành được trích xuất trực tiếp từ mô hình tại `MinhNQ_Day22_model.xlsx`:

| Chỉ số | Giá trị | Ô tham chiếu | Căn cứ tính toán |
| :--- | :---: | :---: | :--- |
| Định nghĩa 1 Job | 1 ca triage hoàn thành | `1_Cost_Job!B5` | Bệnh nhân nhận kết quả phân tầng ESI sơ bộ, hướng dẫn đặt chuyên khoa hoặc điều dưỡng tiếp nhận. |
| Tỷ lệ Containment | 78,00% | `1_Cost_Job!B10` | 78% ca nhẹ và vừa tự xử lý; 22% ca cờ đỏ chuyển tiếp điều dưỡng trực. |
| Cost / Job (COGS) | $0,227 (~5.905 ₫) | `1_Cost_Job!B66` | Bao gồm chi phí LLM có cache, hạ tầng bảo mật dữ liệu y tế, nhân sự HITL (Biến thể B) và 7% retry. |
| Giá sàn (3 × Cost/Job) | $0,681 (~17.715 ₫) | `2_Pricing!B7` | Bội số an toàn tối thiểu bù đắp chi phí R&D, quản trị và rủi ro vận hành. |
| Giá bán đề xuất | $0,790 (~20.540 ₫) | `2_Pricing!B19` | Định giá cạnh tranh với benchmark quốc tế (Intercom Fin $0,99/res; Zendesk $1,50/res). |
| Bội số Giá / Chi phí | 3,48× | `2_Pricing!B20` | Đạt yêu cầu hệ số an toàn (ngưỡng tối thiểu ≥ 3,0×). |
| Gross Margin (GM%) | 71,25% | `2_Pricing!B21` | Nằm trong dải chuẩn của Vertical AI (65–75% theo Bessemer Cloud Index 2024). |
| Breakeven Containment | 70,29% | `2_Pricing!B33` | Ngưỡng containment tối thiểu để duy trì Gross Margin ≥ 60%. Biên an toàn hiện tại là +7,71%. |
| Value Metric | Hybrid | `3_Value_Metric!B30` | Phí nền duy trì hạ tầng ($300/tháng/cơ sở) + $0,79/ca triage hoàn thành. |
| Kênh GTM 90 ngày đầu | Partner-Led | `4_Channel_Fit!B38` | Tích hợp vào Zalo OA Vinmec và ứng dụng MyVinmec (Scorecard 29/30 điểm). |
| Ngân sách CAC tối đa | $10.132 / khách | `4_Channel_Fit!B9` | Tính theo công thức: ARPU ($790) × GM (71,25%) × Payback 18 tháng (phân khúc Mid-market). |
| CAC Sales-Led thực tế | $32.000 / khách | `4_Channel_Fit!B22` | Chi phí cơ hội $8.000 / Win rate 25% (gấp 3,16 lần ngân sách cho phép, không khả thi). |

---

## 2. Trạm 1 — Ngân sách Khách hàng và Định nghĩa Job

### 2.1 Định vị sản phẩm và nguồn ngân sách chi trả

So sánh hai phương án đóng gói sản phẩm:
- Phương án A (Nền tảng / Công cụ): "Nền tảng AI hỗ trợ tiếp nhận bệnh nhân cho cơ sở y tế". Phương án này rơi vào ngân sách CNTT/Phần mềm, phải thẩm định qua nhiều phòng ban kỹ thuật với chu kỳ xét duyệt từ 6 đến 9 tháng.
- Phương án B (Thay thế nghiệp vụ con người — Được chọn): "Trợ lý AI trực ban 24/7 tiếp nhận và phân tầng triệu chứng ban đầu, giảm 80% thời gian chờ đợi tại quầy tiếp đón và giảm tải trực đêm cho điều dưỡng".

Quyết định lựa chọn:
- Nguồn ngân sách: Ngân sách Vận hành và Chăm sóc khách hàng Y tế (Clinical Operations & Patient Intake Budget).
- Cấp phê duyệt: Giám đốc Vận hành (COO) và Giám đốc Chuyên môn Lâm sàng (CMO).
- Lý do: Đóng gói theo nghiệp vụ giảm tải nhân sự trực tiếp giúp khách hàng đo lường hiệu quả nhanh chóng và rút ngắn thời gian chốt hợp đồng xuống dưới 60 ngày.

### 2.2 Tiêu chí xác định 1 Job hoàn thành

Định nghĩa: 1 ca tiếp nhận và phân tầng triệu chứng bệnh nhân hoàn thành là phiên làm việc mà người bệnh nhận được khuyến nghị phân tầng ESI sơ bộ kèm hướng dẫn đặt khám đúng chuyên khoa và xác nhận kết thúc, hoặc được điều dưỡng trực tiếp nhận chuyển giao an toàn kèm bản tóm tắt lâm sàng chuẩn SOAP.

Kiểm chứng 3 tiêu chí:
1. Giá trị khách hàng: Tạo ra giá trị thực cho cơ sở y tế thông qua việc phân loại bệnh nhân trước khi tới khám, giảm tình trạng dồn ứ tại khoa cấp cứu.
2. Khả năng đo lường tự động: Ghi nhận qua mã trạng thái session (`triage_completed` hoặc `escalated_to_nurse_confirmed`).
3. Quy tắc tính phí chặt chẽ: Loại trừ các ca người dùng thoát dưới 2 lượt tương tác (`drop-off`), ca lỗi đường truyền hoặc nội dung spam.

---

## 3. Trạm 2 — Lựa chọn Value Metric

### 3.1 Đánh giá ma trận Attribution × Autonomy

- Điểm Attribution (9 / 10 điểm): Hệ thống lưu vết session ID, bảng câu hỏi triệu chứng, phân tầng nguy cơ ESI (Level 1–5) gắn với mã lịch hẹn trên hệ thống HIS của Vinmec. Tỷ lệ chính xác phân luồng đo lường qua 960 vignette lâm sàng đạt 94,2%.
- Điểm Autonomy (6 / 10 điểm): AI tự động xử lý ca nhẹ và trung bình (cấp độ 4–5). Các ca có dấu hiệu cờ đỏ (cấp độ 1–3) bắt buộc kích hoạt luồng chuyển tiếp sang điều dưỡng trực để bảo đảm an toàn người bệnh.

### 3.2 Quyết định Value Metric và lý do điều chỉnh

- Gợi ý từ mô hình: USAGE (do Attribution ≥ 7 nhưng Autonomy < 7).
- Lựa chọn thực tế: HYBRID (Phí nền hạ tầng cố định + Phí theo ca hoàn thành).
- Lý do điều chỉnh theo thị trường (Market Override): Ngành y tế có yêu cầu khắt khe về an toàn dữ liệu. Khách hàng không chấp nhận thanh toán theo token do khó dự toán ngân sách; ngược lại, đơn vị cung cấp giải pháp không thể chỉ thu theo outcome nếu không có phí duy trì hạ tầng tuân thủ Nghị định 13/2023/NĐ-CP và tiêu chuẩn HIPAA. Mô hình Hybrid ($300/tháng/cơ sở + $0,79/ca hoàn thành) phân bổ rủi ro hợp lý cho cả hai bên.

### 3.3 Dữ liệu đối sánh thị trường (Market Benchmarks)

1. Intercom Fin: Định giá Outcome-based ($0,99 / resolution; chỉ tính phí khi người dùng xác nhận hoặc đóng hội thoại; miễn phí ca chuyển tiếp sang nhân sự). Nguồn: https://fin.ai/pricing/
2. Salesforce Agentforce: Định giá Hybrid ($5/user/tháng + $2,00/conversation hoặc $0,10/action qua Flex Credits). Nguồn: https://www.salesforce.com/agentforce/pricing/
3. Zendesk AI for Healthcare (tham chiếu ngành y tế): Định giá Outcome-based ($1,50 / automated resolution). Nguồn: https://support.zendesk.com/hc/en-us/articles/9570369117338

---

## 4. Trạm 3 — Phân tích Chi phí Cost/Job và Định giá

### 4.1 Cấu trúc 5 thành phần chi phí (1_Cost_Job)

Giả định quy mô vận hành: 5.000 ca triage thử / tháng; tỷ lệ Containment 78% tương ứng 3.900 ca hoàn thành và 1.100 ca chuyển tiếp điều dưỡng.

```
Cost/Job = (LLM + Infra + HITL + Retry + Overhead) / 3.900 ca hoàn thành
```

1. Chi phí LLM (Claude Haiku 4.5 có Prompt Caching):
   - Đơn giá: Input $1,00 / 1M token; Output $5,00 / 1M token; Cache write $1,25 / 1M token; Cache read $0,10 / 1M token.
   - Định mức phiên chat: 5 lượt tương tác; 3.500 token cacheable/lượt; 800 token fresh/lượt; 350 token output/lượt.
   - Chi phí sau cache: $0,0185 / job (giảm 38,8% so với mức không cache $0,0303). Tổng chi phí LLM tháng: $92,63.
2. Chi phí Voice/Speech: $0,00 (tập trung kênh tin nhắn văn bản).
3. Chi phí Hạ tầng (Infra): $0,008 / ca (lưu trữ Vector DB, kết nối cổng HIS, mã hóa dữ liệu y tế). Tổng chi phí tháng: $40,00.
4. Chi phí Retry: Tỷ lệ 7,0% (bù đắp lỗi kết nối và kiểm tra tính hợp lệ dữ liệu). Chi phí tháng: $6,48.
5. Chi phí Nhân sự kiểm soát (HITL — Biến thể B):
   - Chi phí điều dưỡng trực sơ loại tại Việt Nam: $7,00 / giờ fully-loaded (~182.000 ₫/giờ).
   - Kiểm toán QA ngẫu nhiên: 6% ca × 3 phút/ca × $7/giờ = $105,00 / tháng.
   - Điều dưỡng xử lý ca cờ đỏ: 1.100 ca × 5 phút/ca × $7/giờ = $641,67 / tháng.
   - Tổng chi phí HITL tháng: $746,67 (chiếm 84,3% tổng chi phí vận hành).
6. Chi phí Quản lý phân bổ (Overhead): $0,00 (tính toán thuần chi phí trực tiếp COGS).

Tổng chi phí trực tiếp hàng tháng: $885,78.  
Cost/Job hoàn thành: $885,78 / 3.900 = $0,2271 (~5.905 ₫ / ca).

### 4.2 Phương án định giá và phân tích độ nhạy (2_Pricing)

- Giá sàn an toàn (3 × Cost/Job): $3 × 0,2271 = $0,6814 (~17.715 ₫).
- Giá bán đề xuất: $0,7900 (~20.540 ₫ / ca hoàn thành).
- Bội số định giá: 3,48× (đạt điều kiện ≥ 3,0×).
- Gross Margin: 71,25% (đạt ngưỡng an toàn 60–80%).
- Khung giá trần:
  - Neo theo giá trị tạo ra: $4.500 giá trị tiết kiệm và chuyển đổi / 1.000 ca × 25% = $1,125 / job.
  - Neo theo chi phí nhân sự: $1.200 chi phí nhân sự thay thế / 1.000 ca × 70% = $0,840 / job.
  - Mức giá $0,7900 nằm trọn vẹn trong biên giá sàn và giá trần ($0,6814 – $0,8400).
- Điểm hòa vốn Containment (Breakeven): 70,29% (thấp hơn mức vận hành 78,00%).
- Điểm gãy mô hình: Gross Margin giảm xuống dưới 50% khi Containment giảm dưới 63,4% hoặc chi phí API tăng do không áp dụng prompt caching.

---

## 5. Trạm 4 — Lựa chọn Kênh Phân phối (Channel Fit)

### 5.1 Phân tích tính khả thi kênh bán hàng trực tiếp (Sales-Led)

- Doanh thu trung bình trên khách hàng (ARPU): $790 / cơ sở / tháng (tương đương ACV $9.480 / năm).
- Gross Margin: 71,25%.
- Chu kỳ thu hồi vốn CAC tối đa (Phân khúc Mid-market): 18 tháng.
- Ngân sách CAC cho phép: $790 × 71,25% × 18 = $10.132 / khách.
- Chi phí cơ hội bán hàng trực tiếp (theo ICONIQ 2026): $8.000 / cơ hội.
- Tỷ lệ chốt hợp đồng (Win rate): 25,0%.
- CAC thực tế ước tính: $8.000 / 25% = $32.000 / khách.
- Hệ số chênh lệch: $32.000 / $10.132 = 3,16 lần.

Kết luận: Chi phí bán hàng trực tiếp cao gấp 3,16 lần ngân sách cho phép, khẳng định việc xây dựng đội ngũ bán hàng riêng lẻ cho từng phòng khám là không khả thi.

### 5.2 Bảng điểm đánh giá kênh và quyết định chọn Partner-Led

| Tiêu chí đánh giá (Thang điểm 1–5) | PLG | Sales-Led | Partner-Led (Vinmec) |
| :--- | :---: | :---: | :---: |
| 1. Khả năng đáp ứng ngân sách CAC | 2 | 2 | 5 |
| 2. Phù hợp hành vi mua của khách hàng | 2 | 4 | 5 |
| 3. Phù hợp năng lực đội ngũ hiện tại | 3 | 2 | 4 |
| 4. Khả năng tiếp cận đối tác trong 30 ngày | 3 | 2 | 5 |
| 5. Tiếp cận đúng thời điểm người dùng gặp sự cố | 3 | 4 | 5 |
| 6. Khả năng đo lường hiệu quả trong 90 ngày | 3 | 3 | 5 |
| Tổng điểm | 16 / 30 | 17 / 30 | 29 / 30 |

Quyết định kênh: Lựa chọn mô hình Partner-Led hợp tác cùng Hệ thống Y tế Vinmec (7 bệnh viện và 3 phòng khám đa khoa).  
Cơ chế hợp tác: Tích hợp trực tiếp giải pháp vào các kênh số hiện có của Vinmec (Zalo OA và ứng dụng MyVinmec), chia sẻ doanh thu trên từng lượt ca hoàn thành, loại bỏ chi phí tìm kiếm khách hàng mới.

---

## 6. Trạm 5 — Pain Moment và Kế hoạch Triển khai 90 Ngày

### 6.1 Mô tả thời điểm phát sinh nhu cầu (Pain Moment)

- Thời điểm: 21:30 – 23:30 (ngoài giờ hành chính, phòng khám tư đóng cửa).
- Hành vi người dùng: Người bệnh hoặc thân nhân lo lắng khi xuất hiện triệu chứng bất thường, phân vân giữa việc đi cấp cứu đêm hay chờ khám sáng hôm sau.
- Kênh truy cập: Tìm kiếm Zalo Official Account của Vinmec hoặc mở ứng dụng MyVinmec trên điện thoại.
- Điểm tích hợp: Tính năng "Sơ loại triệu chứng 24/7" đặt tại menu Zalo OA Vinmec và mục Tiếp nhận khám trên ứng dụng MyVinmec (người dùng không cần tải ứng dụng mới hay chuyển trang web khác).

### 6.2 Kế hoạch 90 ngày

| Hạng mục | Tháng 1 — Học hỏi | Tháng 2–3 — Tăng tốc | Tháng 4+ — Mở rộng |
| :--- | :--- | :--- | :--- |
| Phạm vi triển khai | Pilot tại BV Vinmec Times City | Mở rộng 7 BV và 3 PK Vinmec | Mở rộng chuỗi y tế tư nhân khác |
| Mục tiêu sản lượng | 1 cơ sở (1.500 ca/tháng) | 10 cơ sở (12.000 ca/tháng) | 3 hệ sinh thái mới (40.000 ca/tháng) |
| Nhiệm vụ kỹ thuật | Tích hợp API vào Zalo OA | Tích hợp sâu HIS/EHR đặt khám | Đóng gói giải pháp chuẩn hóa |
| Nhiệm vụ chuyên môn | Chuẩn hóa 30 kịch bản cờ đỏ lâm sàng | Tối ưu prompt caching lên 80% | Bổ sung theo dõi sau khám |
| Nhiệm vụ quy trình | Thiết lập luồng chuyển tiếp điều dưỡng | Xây dựng dashboard doanh thu | Hoàn thiện hồ sơ Nghị định 13 |
| Chỉ số đo lường (KPI) | Containment 75%, 0 sự cố cờ đỏ | Containment ≥ 78%, GM ≥ 71% | ARR $380.000, Payback < 8 tháng |
| Người phụ trách | Ninh Quang Minh & Trưởng khoa Cấp cứu | Ninh Quang Minh & Đội ngũ HIS | Đội ngũ Kinh doanh & Ninh Quang Minh |

---

## 7. Trạm 6 — Tài liệu Chứng thực (Evidence Pack)

1. Kết quả thử nghiệm lâm sàng (Eval Results):
   - Trạng thái: Đã hoàn thành (07/10/2026).
   - Nội dung: Kiểm thử trên 960 vignette lâm sàng theo giao thức Nature Medicine. Độ chính xác phân tầng 94,2%; tỷ lệ undertriage ca cấp cứu dưới 3,5%; tỷ lệ tự động hoàn thành 78%; tỷ lệ chuyển tiếp an toàn 22%.
2. Báo cáo quản trị rủi ro và tuân thủ (Risk Checklist / Procurement Q&A):
   - Trạng thái: Đã hoàn thành (08/10/2026).
   - Nội dung: Giải quyết 3 yêu cầu của bộ phận mua sắm và an toàn thông tin:
     - Khóa guardrail nghiêm ngặt: không chẩn đoán xác định, chỉ phân tầng nguy cơ; tự động chuyển tuyến khi gặp triệu chứng cờ đỏ.
     - Cam kết Zero Data Retention qua hợp đồng doanh nghiệp với nhà cung cấp mô hình (Azure/Anthropic Enterprise); mã hóa dữ liệu theo chuẩn AES-256 và TLS 1.3.
     - Lưu trữ toàn bộ dữ liệu người dùng tại Private Cloud của Vinmec, bảo đảm tính liên tục hoạt động.
3. Báo cáo thử nghiệm thực tế (Pilot Report):
   - Trạng thái: Chưa hoàn thành.
   - Kế hoạch: Thử nghiệm 4 tuần tại BV Vinmec Times City (quy mô 1.500 ca tiếp nhận, mục tiêu containment 78%, tiết kiệm 200 giờ trực điều dưỡng). Hạn chót: 15/11/2026.

Kết quả kiểm tra tiếp nhận thông tin (Bài test 2 phút): Người đọc trả lời chính xác mục tiêu sản phẩm, đơn vị tính phí, khả năng sinh lời và kênh tiếp cận với 0 câu hỏi phát sinh.

---

## 8. Danh mục Hồ sơ Bài làm

Thư mục bài làm bao gồm các tệp tin theo đúng quy chuẩn đề bài:

1. `MinhNQ_Day22_model.xlsx`: Mô hình tài chính Excel hoàn chỉnh 5 tab làm việc, đã tính toán và kiểm tra toàn bộ công thức, định dạng phông chữ Times New Roman cỡ 13.
2. `MinhNQ_Day22_onepager.pdf`: Văn bản tóm tắt chiến lược định giá và GTM (Monetization One-Pager) định dạng PDF, phông chữ Times New Roman cỡ 13.
3. `MinhNQ_Day22_onepager.docx`: Tệp Word nguồn của tài liệu One-Pager, phông chữ Times New Roman cỡ 13.
4. `README.md`: Báo cáo chi tiết phương án và căn cứ số liệu của toàn bộ bài lab.
