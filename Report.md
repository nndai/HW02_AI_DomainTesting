# Báo cáo HW02 — khung chưa điền

Đây là khung do AI hỗ trợ tạo, chưa phải báo cáo hoàn tất.

## 1. Phạm vi, yêu cầu nguồn và môi trường

Chưa điền. Phân biệt dữ liệu tham khảo trước đây với lần thực hiện mới.

## 2. FR-06 — Chi tiết sản phẩm

Phạm vi kiểm thử là trang chi tiết sản phẩm trên Web, gồm thông tin sản phẩm, số lượng và thao tác thêm vào giỏ hàng. Thiết kế được hỗ trợ bởi [skill domain-testing-bva](skills/domain-testing-bva/SKILL.md).

Quy trình thiết kế: xác định yêu cầu và input/output → phân lớp tương đương → chọn đại diện → phân tích biên → tổng hợp testcase, kết quả và bằng chứng → truy vết và kết luận. Bộ kiểm thử gồm **8 ca F06-D01–D08**.

### 2.1. Yêu cầu, input/output và dữ liệu kiểm thử

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-06. Phương pháp: slide “S04 — Domain Testing”. Các mã R06 dùng để truy vết trong báo cáo.

| Mã     | Yêu cầu FR-06                                                                                |
| ------ | -------------------------------------------------------------------------------------------- |
| R06-01 | Hiển thị đầy đủ ảnh lớn, tên, giá, mô tả và danh mục của sản phẩm.                           |
| R06-02 | Có ô Số lượng, chỉ nhận số nguyên dương, tối thiểu 1.                                        |
| R06-03 | Có thao tác Thêm vào giỏ hàng; sau khi bấm có phản hồi trực quan: toast hoặc badge cập nhật. |

Sau thao tác thêm, đối chiếu sản phẩm và số lượng trong giỏ, đồng thời kiểm tra phản hồi trực quan. Các nghiệp vụ sửa/xóa giỏ, gộp dòng và checkout nằm ngoài phạm vi FR-06.

| ID | Loại / biến | Ràng buộc hoặc kỳ vọng |
| --- | --- | --- |
| I06-P | Input: sản phẩm P được chọn trên Web | P = iPhone 15 Pro Max, ID 1 trên cơ sở dữ liệu seed sạch. |
| I06-S | Input: chuỗi s nhập vào ô Số lượng | Phân biệt nội dung được thử nhập với giá trị thực tế còn lại trong ô. |
| I06-Q | Input: giá trị số lượng q được đối chiếu | Miền hợp lệ: q là số nguyên và q ≥ 1; cận dưới đóng tại 1. |
| I06-A | Input: bấm Thêm vào giỏ một lần | Sau khi bấm có toast notification hoặc badge cập nhật theo R06-03; với q hợp lệ, thêm đúng P/q. |
| S06 | Trạng thái / tiền điều kiện | Đã đăng nhập; giỏ trống; trang P tải xong trước mỗi ca thêm. |
| O06-D | Output: chi tiết sản phẩm | Đủ năm thành phần, nội dung tương ứng dữ liệu P; ảnh lớn của trang chi tiết hiển thị được. |
| O06-A | Output: kết quả thêm và phản hồi | Sau khi bấm có toast notification hoặc badge cập nhật; việc thêm P/q chỉ hợp lệ khi q đáp ứng R06-02. |
| O06-V | Output: kiểm soát q sai | Không dùng chính q không nguyên dương để thêm giỏ, suy từ R06-02. |

**Giới hạn đối chiếu:** SRS không quy định cận trên của số lượng hoặc cơ chế xử lý ô rỗng. Bộ ca không đặt giới hạn tồn kho, giá trị mặc định hay nội dung thông báo cụ thể. Với ô rỗng, đánh giá phản hồi theo R06-03 và số lượng thực tế trong giỏ theo R06-02 nếu có thêm sản phẩm.

#### 2.1.1. Dữ liệu kiểm thử và baseline

P là sản phẩm **iPhone 15 Pro Max**. Dữ liệu kỳ vọng được lấy từ seed của baseline dưới đây, độc lập với nội dung hiển thị của trang đang kiểm thử.

| Thuộc tính               | Giá trị                                                                                     |
| ------------------------ | ------------------------------------------------------------------------------------------- |
| SUT / nền tảng           | EShop, `frontend-web`; mã nguồn tại `HW2/eshop-sut`.                                        |
| Commit nguồn             | `85af3ba875c88283615e22cb108f13e2fccaf0e9`                                                  |
| Nguồn dữ liệu            | `backend/database.js`, dòng 83–103 của commit trên.                                         |
| SHA-256 file seed        | `50012F35C2CF0776DD837482D76ADF701F0EE40CDCB9D80143278FCCDE034717`                          |
| P.ID                     | `1` trên cơ sở dữ liệu được khởi tạo lại từ seed.                                           |
| P.name                   | `iPhone 15 Pro Max`                                                                         |
| P.price                  | `30000000` ₫                                 |
| P.description            | `Điện thoại cao cấp của Apple`                                                              |
| P.category               | `Điện thoại`, `category_id = 1`                                                             |
| P.image                  | `https://placehold.co/300x300/png?text=iPhone+15`; ảnh placeholder có nội dung “iPhone 15”. |
| Đường dẫn sản phẩm       | `/product/1`, theo route `/product/:id` trong `frontend-web/src/App.jsx`.                   |
| Tài khoản thử            | `test@eshop.com`, vai trò người dùng theo seed.                                             |
| Trạng thái trước ca thêm | Đăng nhập hợp lệ; trang P tải xong; giỏ trống trước mỗi ca.                                 |

### 2.2. Phân lớp tương đương

Số lượng hợp lệ thỏa hai điều kiện: **C1** là số nguyên; **C2** là ≥ 1 khi đã là số nguyên. Chuỗi phi số được xét theo kiểu dữ liệu, không so sánh trực tiếp với cận số. Các ca số lượng giữ nguyên P, phiên đăng nhập và trạng thái giỏ ban đầu để cô lập mục tiêu.

| EC-ID | Miền/lớp | Phân loại và kỳ vọng |
|---|---|---|
| E06-P | Sản phẩm P tồn tại, có dữ liệu seed chuẩn | Đầu vào hợp lệ; đối chiếu đủ năm thành phần theo R06-01. |
| E06-Q1 | q là số nguyên, q ≥ 1 | Hợp lệ; thêm đúng P/q và có phản hồi theo R06-02/03. |
| E06-Q2 | q là số nguyên, q ≤ 0 | Không hợp lệ; không thêm với chính số lượng không dương. |
| E06-Q3 | Giá trị số có phần lẻ, đại diện 1.5 | Không hợp lệ do vi phạm C1; không thêm số lượng có phần lẻ. |
| E06-Q4 | Chuỗi không rỗng, không biểu diễn giá trị số, đại diện abc | Không hợp lệ; không dùng chuỗi phi số làm số lượng trong giỏ. |
| X06-EMPTY | Nội dung ô rỗng sau khi xóa trực tiếp | Nhóm bổ sung; đối chiếu phản hồi sau bấm và số lượng trong giỏ nếu có thêm sản phẩm. |

Q1 và Q2 rời nhau, phủ miền số nguyên; Q3 xét số có phần lẻ, Q4 xét chuỗi phi số. X06-EMPTY được tách để phân biệt thao tác xóa trực tiếp với việc thử nhập abc. Bộ ca không khảo sát khoảng trắng, các cách viết số khác hoặc giá trị rất lớn; điều này không thay đổi miền hợp lệ q nguyên, q ≥ 1 theo SRS.

Các lớp đầu ra kỳ vọng được xác định theo biến và điều kiện đầu vào:

| EC-ID | Biến / điều kiện | Lớp đầu ra kỳ vọng |
|---|---|---|
| E06-O1 | O06-D, mở sản phẩm P | Thông tin đủ năm thành phần và đúng dữ liệu P. |
| E06-O2 | O06-A, thêm với q thuộc Q1 | Giỏ có đúng P/q và có toast notification hoặc badge cập nhật. |
| E06-O3 | O06-V và phản hồi, thử dữ liệu thuộc Q2/Q3/Q4 | Không ghi số lượng ngoài miền nguyên dương vào giỏ; có phản hồi sau khi bấm. Việc từ chối dữ liệu không hợp lệ là kết quả đúng kỳ vọng. |

### 2.3. Chọn đại diện

| REP-ID      | Đại diện                         | Phủ / lý do giữ                                                                                  |
| ----------- | -------------------------------- | ------------------------------------------------------------------------------------------------ |
| REP06-P     | iPhone 15 Pro Max theo fixture P | E06-P/O1; một ca đối chiếu đủ năm thành phần và sự hiện diện ô Số lượng/nút Thêm.                |
| REP06-V     | q = 1 và 2                       | E06-Q1/O2; đồng thời phủ tại biên và ngay trên biên. Không thêm ca valid nội miền cùng mục tiêu. |
| REP06-LB    | q = 0                            | E06-Q2/O3; phủ ngay dưới biên.                                                                   |
| REP06-N     | q = -1                           | E06-Q2/O3; kiểm tra số âm trong cùng lớp, không tạo lớp hoặc biên mới.                           |
| REP06-EMPTY | Chuỗi rỗng, độ dài 0             | X06-EMPTY; xóa trực tiếp nội dung ô, khác tình huống abc bị UI chuyển thành rỗng.                |
| REP06-F     | q = 1.5                          | E06-Q3/O3; kiểm tra phần lẻ, giữ các điều kiện khác hợp lệ.                                      |
| REP06-T     | s = abc                          | E06-Q4/O3; kiểm tra sai kiểu qua UI, không ép dữ liệu vượt kênh nhập.                            |

### 2.4. Boundary Value Analysis

**LB = 1, biên đóng; Δ = 1 sản phẩm.** Ba điểm BVA được dùng chung với ca EP, không tạo bộ ca trùng.

| BVA-ID    |    Giá trị | Lớp                 | Ca hiện hành |
| --------- | ---------: | ------------------- | ------------ |
| B06-LOW   | 0 = LB − Δ | E06-Q2, không dương | D04          |
| B06-AT    |     1 = LB | E06-Q1, hợp lệ      | D02          |
| B06-ABOVE | 2 = LB + Δ | E06-Q1, hợp lệ      | D03          |

SRS không quy định UB nên bộ ca chỉ phân tích cận dưới có căn cứ. Giá trị -1 là đại diện bổ sung của Q2; 1.5 và abc kiểm tra điều kiện kiểu/miền; ô rỗng kiểm tra thao tác xóa nội dung. Các ca này không được tính thành điểm BVA mới. Các trường thông tin sản phẩm chỉ được xem, không có thao tác chỉnh sửa để thiết kế biên đầu vào trong FR-06.

### 2.5. Testcase, kết quả và bằng chứng

#### 2.5.1. Tiền điều kiện và tiêu chí đánh giá

**PC:** P theo fixture; đã đăng nhập; giỏ trống; trang chi tiết P tải xong và chưa bấm Thêm trong lượt mở trang đó. Mỗi ca thêm độc lập. Bảng testcase đánh giá một lần bấm; quan sát bấm tiếp lần thứ hai được ghi riêng ở mục 2.5.3.

**R06-03:** Sau một lần bấm, có toast notification hoặc badge cập nhật là đạt mục tiêu phản hồi; không có toast và cũng không có badge cập nhật là không đạt. Tiêu chí áp dụng cho D02–D08 theo câu chữ “sau khi bấm” của FR-06. Chỉ cần một trong hai hình thức, không bắt buộc đồng thời có cả hai.

**R06-02:** Với dữ liệu hợp lệ, đối chiếu sản phẩm và số lượng được thêm. Với dữ liệu không hợp lệ hoặc rỗng, không ghi số lượng ngoài miền nguyên dương vào giỏ. Giỏ trống sau một lần bấm không tự chứng minh cơ chế kiểm tra số lượng hoạt động đúng, vì D02/D03 với số lượng hợp lệ cũng không thêm được. Với D08, không yêu cầu tự điền mặc định hoặc một thông báo lỗi cụ thể.

#### 2.5.2. Bảng testcase

| TC-ID | Mục tiêu / dữ liệu / truy vết | Bước chạy cụ thể | Expected Result | Actual Result | Verdict | Bằng chứng |
|---|---|---|---|---|---|---|
| F06-D01 | Kiểm tra đủ thông tin sản phẩm và điều khiển; P; R06-01/02/03; REP06-P; E06-P/O1 | 1. Chọn P từ danh sách, mở trang chi tiết. 2. Đối chiếu ảnh, tên, giá, mô tả, danh mục với fixture P. 3. Kiểm tra có ô Số lượng và nút Thêm. | Hiển thị đủ ảnh lớn, tên, giá, mô tả và danh mục của P theo dữ liệu chuẩn; có ô Số lượng và nút Thêm vào giỏ hàng. | Ảnh, tên, giá và mô tả đúng fixture; có ô Số lượng và nút Thêm; không thấy danh mục Điện thoại. | FAIL | [Trang chi tiết sản phẩm](screenshots/FR06/D01.png) |
| F06-D02 | Kiểm tra thêm q = 1 và phản hồi toast notification hoặc badge cập nhật; q = 1; R06-02/03; E06-Q1/O2; REP06-V; B06-AT | Thay toàn bộ nội dung ô bằng 1, rời ô, bấm Thêm một lần, quan sát toast notification hoặc badge cập nhật và P/q trong giỏ. | Nhận số lượng 1; thêm đúng P với số lượng 1; có toast notification hoặc badge cập nhật. | Ô giữ 1; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. | FAIL | [Ô số lượng 1](screenshots/FR06/D02-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D02-2.png) |
| F06-D03 | Kiểm tra thêm q = 2 và phản hồi toast notification hoặc badge cập nhật; q = 2; R06-02/03; E06-Q1/O2; REP06-V; B06-ABOVE | Thay toàn bộ nội dung ô bằng 2, rời ô, bấm Thêm một lần, quan sát toast notification hoặc badge cập nhật và P/q trong giỏ. | Nhận số lượng 2; thêm đúng P với số lượng 2; có toast notification hoặc badge cập nhật. | Ô giữ 2; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. | FAIL | [Ô số lượng 2](screenshots/FR06/D03-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D03-2.png) |
| F06-D04 | Kiểm tra số lượng 0 và kiểm tra phản hồi sau bấm Thêm; q = 0; R06-02/03; E06-Q2/O3; REP06-LB; B06-LOW | Thay nội dung ô bằng 0, rời ô; thử Thêm một lần; quan sát giá trị hiệu lực, toast notification/badge và giỏ. | Có toast notification hoặc badge cập nhật sau một lần bấm (R06-03). Không thêm P với số lượng 0 (R06-02). | Ô giữ 0; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. | FAIL | [Ô số lượng 0](screenshots/FR06/D04-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D04-2.png) |
| F06-D05 | Kiểm tra số lượng âm và kiểm tra phản hồi sau bấm Thêm; q = -1; R06-02/03; E06-Q2/O3; REP06-N | Thay nội dung ô bằng -1, rời ô; bấm Thêm một lần; quan sát điểm chặn/giá trị giữ lại, toast notification/badge và giỏ. | Có toast notification hoặc badge cập nhật sau một lần bấm (R06-03). Không thêm P với số lượng -1 (R06-02). | Ô giữ -1; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. | FAIL | [Ô số lượng -1](screenshots/FR06/D05-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D05-2.png) |
| F06-D06 | Kiểm tra số lượng có phần lẻ và kiểm tra phản hồi sau bấm Thêm; q = 1.5; R06-02/03; E06-Q3/O3; REP06-F | Thay nội dung ô bằng 1.5, rời ô; quan sát phần lẻ; bấm Thêm một lần, đối chiếu q và toast notification/badge. | Có toast notification hoặc badge cập nhật sau một lần bấm (R06-03). Không thêm P với số lượng có phần lẻ 1.5 (R06-02); nếu giá trị được chuyển đổi, đối chiếu số lượng thực tế trong giỏ với miền nguyên dương. | Ô giữ 1.5; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. | FAIL | [Ô số lượng 1.5](screenshots/FR06/D06-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D06-2.png) |
| F06-D07 | Kiểm tra chuỗi sai kiểu và kiểm tra phản hồi sau bấm Thêm; s = abc; R06-02/03; E06-Q4/O3; REP06-T | Thay nội dung ô bằng abc, rời ô; bấm Thêm một lần; quan sát dữ liệu giữ lại, toast notification/badge và giỏ. | Có toast notification hoặc badge cập nhật sau một lần bấm (R06-03). Không dùng abc làm số lượng trong giỏ; nếu có thêm sản phẩm, số lượng phải là số nguyên dương (R06-02). | UI không giữ chuỗi abc, ô thành rỗng; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. | FAIL | [Ô số lượng rỗng](screenshots/FR06/D07-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D07-2.png) |
| F06-D08 | Kiểm tra ô rỗng và kiểm tra phản hồi sau bấm Thêm; Chuỗi rỗng, độ dài 0; X06-EMPTY; REP06-EMPTY; R06-02/03 | Chọn toàn bộ nội dung ô rồi Delete, không nhập thay thế; rời ô; bấm Thêm một lần; quan sát giá trị còn lại, toast notification/badge và số lượng trong giỏ nếu có thêm. | Có toast notification hoặc badge cập nhật sau một lần bấm (R06-03). Nếu có thêm sản phẩm, số lượng trong giỏ phải là số nguyên dương (R06-02). | Sau khi xóa toàn bộ nội dung, ô Số lượng vẫn rỗng. Bấm Thêm một lần, không quan sát thấy toast notification và không thấy badge cập nhật. Giỏ hàng trống. | FAIL | [Ô số lượng rỗng](screenshots/FR06/D08-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D08-2.png) |

#### 2.5.3. Quan sát bổ sung sau hai lần bấm

Các quan sát sau được ghi nhận khi bấm tiếp lần thứ hai trên cùng trang; chúng được tách khỏi kết quả một lần bấm trong bảng testcase.

- **F06-D04 — số lượng `0`:** Nhập `0`, ấn hai lần thì vẫn thêm được sản phẩm vào giỏ với số lượng `0`. [Ảnh giỏ có số lượng 0](screenshots/FR06/D04-3.png).
- **F06-D05 — số lượng `-1`:** Nhập `-1`, ấn hai lần thì vẫn thêm được sản phẩm vào giỏ với số lượng `-1`. [Ảnh giỏ có số lượng -1](screenshots/FR06/D08-3.png).
- **F06-D08 — ô rỗng:** Xóa ô nhập cho rỗng, ấn hai lần thì số lượng, thành tiền và tổng tiền trong giỏ hiện `NaN`. [Ảnh giỏ hiển thị NaN](screenshots/FR06/D08-3.png).

### 2.6. Truy vết và kết luận

| Yêu cầu / lớp / biên                                                    | Ca hiện hành    |
| ----------------------------------------------------------------------- | --------------- |
| R06-01; E06-P/O1                                                        | D01             |
| R06-02: có ô Số lượng                                                   | D01             |
| R06-02: E06-Q1; REP06-V                                                 | D02, D03        |
| R06-02: E06-Q2; REP06-LB/N                                              | D04, D05        |
| R06-02: E06-Q3; REP06-F                                                 | D06             |
| R06-02: E06-Q4; REP06-T                                                 | D07             |
| R06-03: có nút Thêm / có toast notification hoặc badge cập nhật sau bấm | D01 / D02–D08   |
| B06-LOW / B06-AT / B06-ABOVE                                            | D04 / D02 / D03 |
| X06-EMPTY; REP06-EMPTY (ô rỗng)                                        | D08             |

Bộ kiểm thử gồm **8 ca = 1 ca thông tin/điều khiển + 2 ca nguyên dương/BVA + 2 ca không dương + 1 ca phần lẻ + 1 ca chuỗi phi số + 1 ca ô rỗng**. Ba điểm BVA là 0–1–2.

**Kết quả: 8 ca đã thực thi, 0 PASS, 8 FAIL.** D01 thiếu danh mục theo R06-01. D02/D03 không thêm sản phẩm với số lượng hợp lệ và không có phản hồi; D04–D08 không có phản hồi sau một lần bấm theo R06-03. Các ghi chú D04/D05/D08 cho thấy số lượng 0, -1 và NaN xuất hiện trong giỏ sau hai lần bấm, không đáp ứng miền nguyên dương của R06-02. Không suy diễn kết quả hai lần bấm cho D06 hoặc D07.

Số testcase FAIL không được dùng làm số bug độc lập. Nhật ký hỗ trợ thiết kế được lưu tại [Appendix_A_Prompt_Log.md](Appendix_A_Prompt_Log.md).

## 3. FR-07 — Giỏ hàng

### 3.1. Yêu cầu, input/output và dữ liệu kiểm thử

Nguồn yêu cầu: [README.md trong eshop-sut](../eshop-sut/README.md), mục SRS, FR-07, dòng 93–100. Phương pháp: [slide S04 — Domain Testing](references/S04_Domain_Testing.txt), bước xác định input/output. Các mã R07 dùng để truy vết trong báo cáo.

| Mã | Yêu cầu FR-07 |
|---|---|
| R07-01 | Hiển thị danh sách sản phẩm với các cột Sản phẩm, Đơn giá, Số lượng, Thành tiền, Thao tác; có nút +/− để chỉnh số lượng. |
| R07-02 | Thêm cùng một sản phẩm làm tăng số lượng, không tạo dòng mới. |
| R07-03 | Nút Xóa sản phẩm phải có dialog xác nhận trước khi thực hiện. |
| R07-04 | Có nút Tiếp tục mua sắm để quay về trang chủ. |
| R07-05 | Tổng tiền dùng nhãn chính xác “Tổng cộng”, không phải “Tổng tạm tính”. |
| R07-06 | Giỏ hàng trống có hình minh họa và thông báo rõ ràng. |

Phạm vi là giỏ hàng trên Web: hiển thị giỏ, điều chỉnh số lượng, thêm trùng sản phẩm, xóa có xác nhận và tiếp tục mua sắm. Sản phẩm được thêm từ luồng mua hàng là đầu vào của giỏ; các nghiệp vụ checkout và mã giảm giá nằm ngoài FR-07.

| ID | Loại / biến | Ràng buộc hoặc kỳ vọng |
|---|---|---|
| I07-P | Input: sản phẩm/dòng được chọn | Xác định sản phẩm mục tiêu của thao tác chỉnh số lượng hoặc xóa; đối chiếu với dữ liệu hiện có trong giỏ. |
| I07-Q | Input: bấm + hoặc − trên dòng sản phẩm | Số lượng có thể được điều chỉnh bằng các nút theo R07-01. |
| I07-ADD | Input: thêm một sản phẩm đã có trong giỏ | Số lượng sản phẩm đó tăng, không tạo dòng mới theo R07-02. |
| I07-DELETE | Input: yêu cầu Xóa và lựa chọn trên dialog | Yêu cầu xóa hiển thị dialog trước thao tác xóa theo R07-03; lựa chọn trên dialog là đầu vào của bước xác nhận. |
| I07-CONTINUE | Input: bấm Tiếp tục mua sắm | Điều hướng về trang chủ theo R07-04. |
| S07-CART | Trạng thái / dữ liệu giỏ | Danh sách sản phẩm, định danh, đơn giá, số lượng hiện tại; trạng thái giỏ trống/có hàng và sản phẩm thêm đã có/chưa có trong giỏ. |
| S07-DIALOG | Trạng thái xác nhận xóa | Phân biệt trước yêu cầu xóa, dialog đang hiển thị và quyết định của người dùng để đối chiếu thứ tự thực hiện. |
| O07-LIST | Output: nội dung giỏ | Hiển thị đủ năm cột và nút +/−; dữ liệu trên từng dòng tương ứng sản phẩm trong giỏ theo R07-01. |
| O07-QUANTITY | Output: số lượng và dòng sản phẩm sau thao tác | Số lượng thể hiện kết quả điều chỉnh; thêm trùng làm tăng số lượng trên dòng hiện có, không tạo dòng mới theo R07-01/02. |
| O07-DELETE | Output: dialog và kết quả thao tác xóa | Dialog xác nhận xuất hiện trước khi xóa sản phẩm mục tiêu theo R07-03. |
| O07-TOTAL | Output: tổng tiền | Nhãn hiển thị là “Tổng cộng” theo R07-05. |
| O07-EMPTY | Output: giỏ trống | Có hình minh họa và thông báo rõ ràng theo R07-06. |
| O07-NAV | Output: trang sau Tiếp tục mua sắm | Hiển thị trang chủ theo R07-04. |

**Giới hạn đối chiếu:** FR-07 chưa quy định miền số lượng, bước tăng/giảm, xử lý số lượng 0, nhập trực tiếp hoặc giới hạn tồn kho; không tự áp min 1 của FR-06 hay đặt cận trên cho giỏ. Công thức thành tiền, tổng tiền, lượng cộng khi thêm trùng và quy tắc làm tròn chưa được nêu cụ thể. Nội dung dialog và hành vi hủy cũng chưa được mô tả chi tiết. Thiết kế chưa đặt kỳ vọng định lượng hoặc cơ chế xử lý cụ thể cho các nội dung này.

### 3.2. Phân lớp tương đương

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-07. Phương pháp: slide “S04 — Domain Testing”. Các lớp được xác định theo input/output và trạng thái ở mục 3.1; hai tình huống cùng lớp có cùng cách xử lý hoặc cùng loại kết quả kỳ vọng, không nhất thiết có cùng giá trị số lượng hay số tiền.

Các lớp input và trạng thái được chia riêng theo từng biến/điều kiện. Một tình huống có thể đồng thời thuộc lớp giỏ có hàng, sản phẩm đã có trong giỏ và thao tác tăng; đây là sự kết hợp giữa các biến, không phải chồng lấn trong cùng một phép phân hoạch.

| EC-ID | Biến / điều kiện | Miền/lớp | Phân loại và kỳ vọng |
|---|---|---|---|
| E07-C0 | S07-CART, trạng thái giỏ | Giỏ không chứa sản phẩm | **Trạng thái hợp lệ**; hiển thị hình minh họa và thông báo giỏ trống rõ ràng theo R07-06. Không coi thiếu sản phẩm là đầu vào lỗi. |
| E07-C1 | S07-CART, trạng thái giỏ | Giỏ chứa sản phẩm | Trạng thái hợp lệ; hiển thị các dòng sản phẩm, đủ năm cột và nút +/− theo R07-01; nhãn tổng tiền là “Tổng cộng” theo R07-05. |
| E07-P1 | I07-ADD và S07-CART, quan hệ sản phẩm thêm với giỏ trước thao tác | Sản phẩm thêm đã có trong giỏ | Đầu vào/trạng thái hợp lệ; tăng số lượng trên dòng hiện có, không tạo dòng mới cho cùng sản phẩm theo R07-02. Không đặt lượng tăng cụ thể. |
| E07-P2 | I07-ADD và S07-CART, cùng điều kiện nhận diện sản phẩm | Sản phẩm thêm chưa có trong giỏ | Trạng thái đầu vào hợp lệ để chuẩn bị giỏ; không áp kỳ vọng gộp vào dòng cũ của R07-02. Khi giỏ có sản phẩm, đối chiếu hiển thị theo R07-01/05; FR-07 không mô tả riêng chi tiết xử lý thao tác thêm mới. |
| E07-QPLUS | I07-Q, thao tác chỉnh số lượng trên dòng đang có | Bấm nút + | Thao tác hợp lệ được FR-07 nêu; số lượng có thể được điều chỉnh theo hướng tăng theo R07-01. Chưa xác định mức tăng hoặc phản ứng tại giới hạn số lượng chưa được đặc tả. |
| E07-QMINUS | I07-Q, cùng điều kiện chọn dòng | Bấm nút − | Thao tác hợp lệ được FR-07 nêu; số lượng có thể được điều chỉnh theo hướng giảm theo R07-01. Chưa đặt cận dưới, lượng giảm hoặc hành vi khi giảm tới giới hạn chưa được đặc tả. |
| E07-DEL | I07-DELETE, yêu cầu thao tác trên sản phẩm đang có | Bấm Xóa sản phẩm mục tiêu | Thao tác hợp lệ; xuất hiện dialog xác nhận trước khi thực hiện xóa theo R07-03. |
| E07-D0 | S07-DIALOG, sản phẩm mục tiêu đang có | Dialog chưa hiển thị trước yêu cầu xóa | Trạng thái hợp lệ; yêu cầu Xóa phải đi qua bước hiển thị dialog trước thao tác xóa theo R07-03. |
| E07-D1 | S07-DIALOG, cùng thao tác xóa | Dialog xác nhận đang hiển thị, chưa có quyết định | Trạng thái hợp lệ của bước xác nhận; đối chiếu thứ tự dialog xuất hiện trước thao tác xóa theo R07-03. Không gán sẵn kết quả cho lựa chọn hủy/đóng chưa được mô tả. |
| E07-NAV | I07-CONTINUE | Bấm Tiếp tục mua sắm | Thao tác hợp lệ; điều hướng về trang chủ theo R07-04, không tự đặt URL đích cụ thể. |

C0/C1 phân biệt giỏ trống và có hàng; P1/P2 phân biệt quan hệ đã có/chưa có của sản phẩm được thêm; QPLUS/QMINUS phân biệt hai thao tác chỉnh số lượng. D0/D1 xét trạng thái dialog trong luồng xóa sản phẩm đang có. Các điều kiện này thuộc các biến và thời điểm khác nhau; không dùng chúng như một danh sách lớp loại trừ nhau cho toàn bộ giỏ hàng.

Các lớp đầu ra kỳ vọng được xác định theo điều kiện đầu vào và trạng thái tương ứng:

| EC-ID | Biến / điều kiện | Lớp đầu ra kỳ vọng |
|---|---|---|
| E07-OEMPTY | O07-EMPTY, giỏ thuộc C0 | Hình minh họa và thông báo rõ ràng cho giỏ trống theo R07-06; đây là đầu ra đúng của trạng thái hợp lệ. Không yêu cầu một ảnh hoặc nguyên văn thông báo cụ thể. |
| E07-OLIST | O07-LIST, giỏ thuộc C1 | Danh sách phản ánh dữ liệu giỏ với đủ Sản phẩm, Đơn giá, Số lượng, Thành tiền, Thao tác và nút +/− theo R07-01. |
| E07-OINCREASE | O07-QUANTITY, chỉnh số lượng bằng QPLUS | Số lượng thể hiện kết quả tăng theo R07-01; mức tăng và cách xử lý giới hạn chưa có oracle định lượng. |
| E07-ODECREASE | O07-QUANTITY, chỉnh số lượng bằng QMINUS | Số lượng thể hiện kết quả giảm theo R07-01; mức giảm và cách xử lý giới hạn chưa có oracle định lượng. |
| E07-ODUPLICATE | O07-QUANTITY, thêm sản phẩm thuộc P1 | Số lượng của sản phẩm đã có tăng và không tạo dòng mới theo R07-02. Không đồng nhất kỳ vọng này với việc thêm sản phẩm khác. |
| E07-ODELETE | O07-DELETE, yêu cầu DEL trong luồng D0 sang D1 | Có dialog xác nhận trước khi xóa sản phẩm mục tiêu theo R07-03; không tự quy định nội dung/nút hoặc hành vi hủy. |
| E07-OTOTAL | O07-TOTAL, hiển thị tổng tiền giỏ | Nhãn là “Tổng cộng”, không phải “Tổng tạm tính”, theo R07-05. Chưa đặt công thức hoặc giá trị tổng cụ thể. |
| E07-ONAV | O07-NAV, thao tác NAV | Trang chủ được hiển thị theo R07-04. |

**Giới hạn phân lớp:** FR-07 chưa đủ căn cứ để chia miền số lượng thành các lớp valid/invalid theo min/max, kiểu số, ô rỗng hoặc nhập trực tiếp; không kế thừa các cận của FR-06. Các lựa chọn xác nhận/hủy/đóng dialog được nhận diện là đầu vào của bước xác nhận, nhưng chưa tách thành lớp có kỳ vọng xử lý chi tiết khi đặc tả chưa nêu. Giỏ trống không thuộc lớp invalid; nhãn sai, thiếu cột hoặc thêm trùng thành dòng mới là đầu ra không đáp ứng yêu cầu tương ứng, không phải lý do đổi phân loại đầu vào thành invalid.

### 3.3. Chọn đại diện

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-07. Nguồn sản phẩm: file `backend/database.js`, cùng baseline seed với FR-06. Phương pháp: slide “S04 — Domain Testing”, chọn đại diện và kết hợp các lớp hợp lệ.

| Ký hiệu | ID trên seed sạch | Tên sản phẩm | Đơn giá theo seed | Danh mục |
|---|---|---|---:|---|
| P | 1 | iPhone 15 Pro Max | 30000000 ₫ | Điện thoại |
| Q | 2 | Samsung Galaxy S24 Ultra | 28000000 ₫ | Điện thoại |

**Tiền điều kiện chung:** Web dùng baseline nêu tại mục 2.1.1; phiên đã đăng nhập bằng tài khoản `test@eshop.com`. Giỏ và số lượng dưới đây là dữ liệu cần chuẩn bị cho từng đại diện, không phải dữ liệu giỏ có sẵn trong seed hoặc trạng thái đã được xác minh. Chọn P có số lượng 3 và Q có số lượng 2 làm mẫu cụ thể để dễ đối chiếu hai dòng khác nhau; các số này không xác lập miền hợp lệ, min/max hoặc điểm BVA của FR-07.

| REP-ID | Dữ liệu và thao tác đại diện | Lớp được phủ | Kỳ vọng theo yêu cầu | Lý do chọn / giới hạn |
|---|---|---|---|---|
| REP07-EMPTY | Giỏ trống, n = 0; mở giỏ để đối chiếu đầu ra trống. | E07-C0, E07-OEMPTY | Hình minh họa và thông báo giỏ trống rõ ràng (R07-06). | Gộp EP giỏ trống với điểm B07-EMPTY trong F07-D01. Điều hướng được đánh giá riêng bằng giỏ có hàng. |
| REP07-CART | Giỏ chỉ có P:3 hoặc có P:3, Q:2. Trạng thái hai dòng được chuẩn bị với Q chưa có trong giỏ trước khi thêm. Kiểm tra danh sách/nhãn; mỗi thao tác + hoặc − bắt đầu riêng từ giỏ P:3, Q:2. | E07-P2 (chuẩn bị), E07-C1, E07-OLIST, E07-OTOTAL; E07-QPLUS, E07-OINCREASE; E07-QMINUS, E07-ODECREASE | Đủ năm cột và nút +/−; dữ liệu tương ứng giỏ; nhãn “Tổng cộng”; chỉnh số lượng theo hướng tăng/giảm (R07-01/05). | Dùng chung dữ liệu cho F07-D02–D06, giữ kết quả riêng cho hiển thị, nhãn, tăng và giảm. Hai trạng thái một/hai dòng dùng chung với BVA. |
| REP07-DUP | Tiền điều kiện: giỏ đã có P:3, Q:2. Thêm lại cùng sản phẩm P, sau đó đối chiếu số lượng và số dòng của P trong giỏ. | E07-P1, E07-ODUPLICATE | Số lượng P tăng so với 3; P vẫn chỉ có một dòng (R07-02). | Cùng ID P đại diện thêm trùng; Q phân biệt với sản phẩm khác. Dùng F07-D07; không tự đặt lượng cộng chính xác. |
| REP07-DELETE | Giỏ P:3, Q:2; dialog chưa mở. Bấm Xóa trên dòng P và quan sát trước khi quyết định xác nhận. | E07-DEL, E07-D0, E07-D1, E07-ODELETE | Dialog xác nhận xuất hiện trước khi xóa P (R07-03). | Gộp hai trạng thái dialog và đầu ra của cùng yêu cầu xóa trong F07-D08. |
| REP07-NAV | Giỏ P:3, Q:2; bấm Tiếp tục mua sắm. | E07-NAV, E07-ONAV | Hiển thị trang chủ (R07-04). | F07-D09 đánh giá điều hướng riêng, không phụ thuộc kết quả giỏ trống. |

Năm nhóm đại diện được tổng hợp thành bảng testcase ở mục 3.5. Các lớp đầu ra dùng chung ca với input/trạng thái tạo ra chúng; E07-P2 là điều kiện chuẩn bị dữ liệu, không có ca đánh giá riêng luồng thêm mới trong FR-07.

### 3.4. Boundary Value Analysis

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-07. Phương pháp: slide “S04 — Domain Testing”. Dữ liệu P/Q và tiền điều kiện đăng nhập dùng lại mục 3.3.

**Biến áp dụng:** n là số dòng sản phẩm phân biệt trong giỏ (thuộc S07-CART), đơn vị là dòng, n là số nguyên không âm. Giỏ chỉ có P với số lượng 3 vẫn có n = 1, không phải 3 dòng. Hai lớp có căn cứ là E07-C0: n = 0 và E07-C1: n ≥ 1; cả hai đều hợp lệ.

Chuyển tiếp cần xét là **0 ↔ 1 dòng**. Với lớp có hàng, cận dưới đóng LB = 1 dòng, bước lân cận Δ = 1 dòng; chọn 0–1–2 tương ứng ngay dưới, tại và ngay trên cận này. **0 dòng thuộc lớp giỏ trống hợp lệ**, không phải đầu vào lỗi. Số dòng âm không thể tạo qua thao tác giỏ trên Web nên không sinh ca n = −1 hoặc kỳ vọng thông báo lỗi cho giá trị đó. FR-07 chưa quy định cận trên số dòng; số sản phẩm trong seed không được dùng làm max của giỏ.

| BVA-ID | Giá trị / dữ liệu cần chuẩn bị | Lớp được phủ | Kỳ vọng theo yêu cầu | Đại diện / ca hiện hành |
|---|---|---|---|---|
| B07-EMPTY | n = 0 = LB − Δ; giỏ không chứa sản phẩm | E07-C0, E07-OEMPTY | Có hình minh họa và thông báo giỏ trống rõ ràng (R07-06); không đặt nguyên văn thông báo hoặc ảnh cụ thể. | REP07-EMPTY; F07-D01. |
| B07-FIRST | n = 1 = LB; giỏ chỉ có P:3, P là iPhone 15 Pro Max theo seed | E07-C1, E07-OLIST, E07-OTOTAL | Hiển thị dòng P đúng dữ liệu đã chuẩn bị, đủ năm cột và nút +/− (R07-01); nhãn tổng tiền là “Tổng cộng” (R07-05). | REP07-CART, trạng thái chỉ có P; F07-D02 (danh sách), F07-D04 (nhãn). |
| B07-NEXT | n = 2 = LB + Δ; giỏ P:3, Q:2 theo seed | E07-C1, E07-OLIST, E07-OTOTAL | Hiển thị hai dòng P/Q tương ứng dữ liệu đã chuẩn bị, đủ năm cột và nút +/− (R07-01); nhãn “Tổng cộng” (R07-05). | REP07-CART, trạng thái P/Q; F07-D03 (danh sách), F07-D04 (nhãn). |

Ba điểm trên phân biệt đầu ra giỏ trống với đầu ra có hàng tại chuyển tiếp 0 ↔ 1, đồng thời kiểm tra điểm kế tiếp trong lớp có hàng. Không mặc định bấm − làm mất dòng hoặc đưa giỏ về trống; kiểm tra trình tự dialog trước khi xóa vẫn thuộc REP07-DELETE.

**Phần không áp dụng hoặc chưa đủ căn cứ:**

| Biến / mục tiêu | Quyết định và lý do |
|---|---|
| Số lượng trên mỗi dòng, thao tác +/− và lượng cộng khi thêm trùng | Chưa đủ căn cứ đặt biên số lượng: FR-07 chưa quy định min/max, bước tăng/giảm hoặc xử lý tại 0. Giữ các đại diện EP ở mục 3.3; không lấy min 1 của FR-06 làm biên của FR-07. |
| Sản phẩm đã có/chưa có trong giỏ | Quan hệ định danh không có thứ tự số để áp BVA. Kiểm tra gộp dòng bằng REP07-DUP theo R07-02; ID và giá của P/Q không trở thành biên. |
| Yêu cầu Xóa, trạng thái dialog và Tiếp tục mua sắm | Các thao tác/trạng thái được phân biệt theo loại và trình tự, không phải khoảng số có cận. Giữ kiểm tra EP tương ứng, không gán số để tạo BVA. |
| Nhãn tổng tiền, thành tiền và tổng tiền | Nhãn là nội dung cần đối chiếu theo R07-05, không có biên độ dài được quy định. FR-07 chưa nêu công thức, giới hạn tiền hoặc quy tắc làm tròn để thiết kế biên tiền; chưa đặt giá trị tổng cụ thể. |

### 3.5. Testcase, kết quả và bằng chứng

#### 3.5.1. Tiền điều kiện và tiêu chí đánh giá

**PC chung:** Web và dữ liệu P/Q theo mục 3.3; đã đăng nhập bằng tài khoản `test@eshop.com`; trang giỏ tải xong. Chuẩn bị đúng trạng thái trước mỗi ca; không dùng kết quả của ca trước làm tiền điều kiện cho ca sau.

| Mã PC | Trạng thái giỏ trước ca |
|---|---|
| PC0 | Giỏ trống, n = 0. |
| PC1 | Giỏ đã có đúng một dòng P (ID 1, iPhone 15 Pro Max), đơn giá 30000000 ₫, số lượng 3; n = 1. |
| PC2 | Giỏ đã có đúng hai dòng: P như PC1 và Q (ID 2, Samsung Galaxy S24 Ultra), đơn giá 28000000 ₫, số lượng 2; n = 2. |

PC1/PC2 là tiền điều kiện **giỏ đã có hàng**, không phải kết quả cần tạo trong các bước đánh giá hiển thị, chỉnh số lượng, thêm trùng, xóa hoặc điều hướng. E07-P2 chỉ nằm ở khâu chuẩn bị PC2 từ giỏ có P nhưng chưa có Q; không đặt oracle riêng cho thao tác thêm sản phẩm mới.

**Tiêu chí:** So sánh số lượng sau +/− với giá trị trước thao tác, không mặc định bước ±1 hoặc giới hạn số lượng. Thêm trùng đánh giá đồng thời tăng số lượng và giữ một dòng cho P theo R07-02, không đặt kết quả phải bằng 5. Thành tiền/tổng tiền không có kỳ vọng số cụ thể; nhãn được đánh giá riêng. Ca xóa chỉ đánh giá dialog xuất hiện trước khi xóa, không tự đặt nội dung dialog hoặc kết quả hủy/đóng. Với giỏ trống, không yêu cầu nguyên văn thông báo hay ảnh cụ thể.

#### 3.5.2. Bảng testcase

| TC-ID | Mục tiêu / dữ liệu / truy vết | Bước chạy cụ thể | Expected Result | Actual Result | Verdict | Bằng chứng |
|---|---|---|---|---|---|---|
| F07-D01 | Giỏ trống hợp lệ; PC0, n = 0; R07-06; REP07-EMPTY; E07-C0, E07-OEMPTY; B07-EMPTY | 1. Mở giỏ với PC0. 2. Quan sát hình minh họa và thông báo trạng thái trống. | Có hình minh họa và thông báo rõ ràng cho giỏ trống. 0 dòng là trạng thái hợp lệ. | Hiển thị “Giỏ hàng của bạn đang trống” và liên kết “Tiếp tục mua sắm”; không thấy hình minh họa/icon cho trạng thái trống. | FAIL | [Giỏ trống thiếu hình minh họa](screenshots/FR07/D01.png) |
| F07-D02 | Hiển thị tại một dòng; PC1, P:3; R07-01; REP07-CART; E07-C1, E07-OLIST; B07-FIRST | 1. Mở giỏ với PC1. 2. Đếm dòng, đối chiếu tên P, đơn giá 30000000 ₫ và số lượng 3. 3. Kiểm tra năm cột và nút +/−. | Có một dòng P đúng tên, đơn giá và số lượng đã chuẩn bị; đủ Sản phẩm, Đơn giá, Số lượng, Thành tiền, Thao tác; có nút +/−. | Có 1 dòng iPhone 15 Pro Max, đơn giá 30.000.000 ₫, số lượng 3, thành tiền 90.000.000 ₫. Có các cột Sản phẩm, Giá, Số lượng, Thành tiền, Thao tác; không có nút +/− trên dòng P. | FAIL | [Giỏ một dòng thiếu nút +/−](screenshots/FR07/D02.png) |
| F07-D03 | Hiển thị tại hai dòng; PC2, P:3 và Q:2; R07-01; REP07-CART; E07-C1, E07-OLIST; B07-NEXT | 1. Mở giỏ với PC2. 2. Đếm dòng; đối chiếu P/30000000 ₫/3 và Q/28000000 ₫/2. 3. Kiểm tra năm cột và nút +/− trên từng dòng. | Có hai dòng P/Q với tên, đơn giá, số lượng đúng dữ liệu đã chuẩn bị; đủ năm cột và nút +/−. | Có 2 dòng: iPhone 15 Pro Max — đơn giá 30.000.000 ₫, q=3, thành tiền 90.000.000 ₫; Samsung Galaxy S24 Ultra — đơn giá 28.000.000 ₫, q=2, thành tiền 56.000.000 ₫. Có 5 cột nhưng không có nút +/− trên cả hai dòng. | FAIL | [Giỏ hai dòng thiếu nút +/−](screenshots/FR07/D03.png) |
| F07-D04 | Nhãn tổng tiền; lần lượt PC1 và PC2; R07-05; REP07-CART; E07-OTOTAL; B07-FIRST, B07-NEXT | 1. Mở giỏ đã chuẩn bị PC1, đọc nguyên văn nhãn tổng tiền. 2. Chuẩn bị riêng PC2, mở giỏ và đọc lại nhãn. Có thể dùng chung lượt quan sát trang với D02/D03 nhưng ghi kết quả nhãn ở ca này. | Ở cả trạng thái một dòng và hai dòng, nhãn hiển thị chính xác “Tổng cộng”, không phải “Tổng tạm tính”. | PC1 hiển thị “Tổng tạm tính: 90.000.000 ₫”; PC2 hiển thị “Tổng tạm tính: 146.000.000 ₫”. Cả hai đều không dùng nhãn “Tổng cộng”. | FAIL | [Nhãn ở giỏ một dòng](screenshots/FR07/D04-2.png)<br>[Nhãn ở giỏ hai dòng](screenshots/FR07/D04-1.png) |
| F07-D05 | Tăng số lượng; PC2, dòng P có số lượng 3; R07-01; REP07-CART; E07-QPLUS, E07-OINCREASE | 1. Mở giỏ với PC2, đối chiếu số lượng P trước thao tác là 3. 2. Bấm + trên dòng P một lần. 3. Quan sát số lượng P sau thao tác. | Số lượng P tăng so với 3; không đặt mức tăng cụ thể. | Giỏ đúng PC2, P có số lượng 3. Không có nút + trên dòng P hoặc trong giỏ; không thể thực hiện bước bấm tăng. Không ghi nhận kết quả tăng sau thao tác vì thao tác không thực hiện được. | FAIL | [Không có nút tăng số lượng](screenshots/FR07/D05.png) |
| F07-D06 | Giảm số lượng; PC2 được chuẩn bị lại, P:3; R07-01; REP07-CART; E07-QMINUS, E07-ODECREASE | 1. Mở giỏ với đúng PC2, số lượng P là 3. 2. Bấm − trên dòng P một lần. 3. Quan sát số lượng P sau thao tác. | Số lượng P giảm so với 3; không đặt mức giảm, min hoặc cơ chế xử lý tại giới hạn. | Giỏ đúng PC2, P có số lượng 3. Không có nút − trên dòng P hoặc trong giỏ; không thể thực hiện bước bấm giảm. Không ghi nhận kết quả giảm sau thao tác vì thao tác không thực hiện được. | FAIL | [Không có nút giảm số lượng](screenshots/FR07/D06.png) |
| F07-D07 | Thêm trùng đúng P; PC2, P ban đầu có một dòng với số lượng 3; R07-02; REP07-DUP; E07-P1, E07-ODUPLICATE | 1. Mở giỏ với PC2, ghi nhận P đang có một dòng với số lượng 3. 2. Thực hiện thao tác thêm lại đúng sản phẩm P qua luồng mua hàng. 3. Trở lại giỏ, đếm số dòng của P và đối chiếu số lượng P với 3 trước thao tác. | Số lượng trên dòng P tăng so với 3, P vẫn chỉ có một dòng; không tạo dòng P mới. | Sau khi lượt thêm được xử lý, giỏ có 3 dòng: P q=3 (90.000.000 ₫), Q q=2 (56.000.000 ₫), và một dòng P mới q=2 (60.000.000 ₫). P xuất hiện thành hai dòng riêng, dòng P ban đầu vẫn q=3; không gộp vào dòng hiện có. Lượt thêm thực tế dùng lượng nhập 2 và cần hai lần bấm do lỗi FR-06 đã biết. | FAIL | [P xuất hiện thành hai dòng riêng](screenshots/FR07/D07.png) |
| F07-D08 | Dialog trước khi xóa P; PC2, dialog chưa mở; R07-03; REP07-DELETE; E07-DEL, E07-D0, E07-D1, E07-ODELETE | 1. Mở giỏ với PC2, xác định dòng P và trạng thái chưa có dialog. 2. Bấm Xóa sản phẩm trên dòng P một lần. 3. Quan sát dialog và thời điểm xóa P; chưa chọn xác nhận/hủy/đóng. | Có dialog xác nhận trước khi thực hiện xóa P; không xóa P trước bước xác nhận. | Ngay sau một lần bấm Xóa trên P, dòng P biến mất; giỏ chỉ còn Q q=2. Không xuất hiện dialog xác nhận và không thực hiện lựa chọn xác nhận/hủy/đóng nào. | FAIL | [Giỏ sau khi xóa P, chỉ còn Q](screenshots/FR07/D08.png) |
| F07-D09 | Tiếp tục mua sắm; PC2; R07-04; REP07-NAV; E07-NAV, E07-ONAV | 1. Mở giỏ với PC2. 2. Bấm Tiếp tục mua sắm một lần. 3. Quan sát trang được hiển thị. | Hiển thị trang chủ; không đặt URL đích cụ thể. | Giỏ có liên kết “← Mua tiếp”. Bấm một lần điều hướng đến http://localhost:5173/ và hiển thị trang chủ “Danh sách sản phẩm”. | PASS |  |

### 3.6. Truy vết và kết luận

| Yêu cầu / lớp / biên | Ca hiện hành |
|---|---|
| R07-01: E07-C1, E07-OLIST; REP07-CART | F07-D02, F07-D03 |
| R07-05: E07-OTOTAL; REP07-CART | F07-D04, đối chiếu ở cả PC1 và PC2 |
| R07-01: E07-QPLUS, E07-OINCREASE; REP07-CART | F07-D05 |
| R07-01: E07-QMINUS, E07-ODECREASE; REP07-CART | F07-D06 |
| R07-02: E07-P1, E07-ODUPLICATE; REP07-DUP | F07-D07 |
| R07-03: E07-DEL, E07-D0, E07-D1, E07-ODELETE; REP07-DELETE | F07-D08 |
| R07-04: E07-NAV, E07-ONAV; REP07-NAV | F07-D09 |
| R07-06: E07-C0, E07-OEMPTY; REP07-EMPTY | F07-D01 |
| B07-EMPTY / B07-FIRST / B07-NEXT | F07-D01 / F07-D02 và D04 / F07-D03 và D04 |
| E07-P2: sản phẩm Q chưa có trước khâu chuẩn bị PC2 | Điều kiện chuẩn bị cho D03 và các ca dùng PC2; không có ca đánh giá riêng thao tác thêm mới |

Bộ thiết kế gồm **9 ca F07-D01–D09**. EP và BVA dùng chung ba trạng thái 0–1–2 dòng; không sinh bộ ca biên trùng với ca EP. Nhãn tổng tiền dùng chung lượt quan sát với hai ca danh sách nhưng có dòng kết quả riêng; tăng và giảm bắt đầu độc lập từ PC2. Hai trạng thái dialog thuộc cùng một yêu cầu xóa nên dùng chung D08. Điều hướng D09 dùng giỏ có hàng và tách khỏi D01 để kết quả không phụ thuộc mục tiêu giỏ trống.

Giới hạn thiết kế giữ theo mục 3.1 và 3.4: không đặt min/max số lượng, bước tăng/giảm, giới hạn tồn kho, công thức tiền hoặc hành vi hủy/đóng dialog ngoài đặc tả.

## 4. FR-14 — Danh mục Admin

### 4.1. Input/output và yêu cầu nguồn

Chưa thực hiện.

### 4.2. Equivalence partitions

Chưa thực hiện.

### 4.3. Đại diện và test cases

Chưa thực hiện. Không điền Actual/Verdict trước khi chạy.

### 4.4. Boundary Value Analysis

Chưa thực hiện.

### 4.5. Đánh giá thiết kế

Chưa thực hiện.

### 4.6. Thực thi và bằng chứng

Chưa thực hiện trong quy trình mới.

### 4.7. Liên kết bug report và GitHub Issues

Liên kết báo cáo lỗi và GitHub Issues được bổ sung sau khi hoàn thành báo cáo lỗi và tạo issue.

### 4.8. AI audit và gap quan sát được

Chưa đánh giá. Không dựng thiếu sót AI.

## 5. FR-20 — Hồ sơ Mobile

### 5.1. Input/output và yêu cầu nguồn

Chưa thực hiện.

### 5.2. Equivalence partitions

Chưa thực hiện.

### 5.3. Đại diện và test cases

Chưa thực hiện. Không điền Actual/Verdict trước khi chạy.

### 5.4. Boundary Value Analysis

Chưa thực hiện.

### 5.5. Đánh giá thiết kế

Chưa thực hiện.

### 5.6. Thực thi và bằng chứng

Chưa thực hiện trong quy trình mới.

### 5.7. Liên kết bug report và GitHub Issues

Liên kết báo cáo lỗi và GitHub Issues được bổ sung sau khi hoàn thành báo cáo lỗi và tạo issue.

### 5.8. AI audit và gap quan sát được

Chưa đánh giá. Không dựng thiếu sót AI.

## 6. Tổng kết

Chưa điền.

## 7. Mandatory Disclosure

Chưa điền. Dùng mẫu trong AI-02 và phản ánh đúng phạm vi hỗ trợ AI và phân công thực hiện.
