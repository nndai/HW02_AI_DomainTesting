# Báo cáo HW02 — Domain Testing và Boundary Value Analysis

**Sinh viên:** Nguyễn Ngọc Đại — **MSSV:** 23120226. **SUT:** EShop.

## 1. FR-06 — Chi tiết sản phẩm

Phạm vi kiểm thử là trang chi tiết sản phẩm trên Web, gồm thông tin sản phẩm, số lượng và thao tác thêm vào giỏ hàng. Thiết kế được hỗ trợ bởi [skill domain-testing-bva](skills/domain-testing-bva/SKILL.md).

Quy trình thiết kế: xác định yêu cầu và input/output → phân lớp tương đương → chọn đại diện → phân tích biên → tổng hợp testcase, kết quả và bằng chứng → truy vết và kết luận. Bộ kiểm thử gồm **8 ca F06-D01–D08**.

### 1.1. Yêu cầu và dữ liệu

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

**Dữ liệu kiểm thử:**

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

### 1.2. Phân lớp (EP)

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

### 1.3. Đại diện

| REP-ID      | Đại diện                         | Phủ / lý do giữ                                                                                  |
| ----------- | -------------------------------- | ------------------------------------------------------------------------------------------------ |
| REP06-P     | iPhone 15 Pro Max theo fixture P | E06-P/O1; một ca đối chiếu đủ năm thành phần và sự hiện diện ô Số lượng/nút Thêm.                |
| REP06-V     | q = 1 và 2                       | E06-Q1/O2; đồng thời phủ tại biên và ngay trên biên. Không thêm ca valid nội miền cùng mục tiêu. |
| REP06-LB    | q = 0                            | E06-Q2/O3; phủ ngay dưới biên.                                                                   |
| REP06-N     | q = -1                           | E06-Q2/O3; kiểm tra số âm trong cùng lớp, không tạo lớp hoặc biên mới.                           |
| REP06-EMPTY | Chuỗi rỗng, độ dài 0             | X06-EMPTY; xóa trực tiếp nội dung ô, khác tình huống abc bị UI chuyển thành rỗng.                |
| REP06-F     | q = 1.5                          | E06-Q3/O3; kiểm tra phần lẻ, giữ các điều kiện khác hợp lệ.                                      |
| REP06-T     | s = abc                          | E06-Q4/O3; kiểm tra sai kiểu qua UI, không ép dữ liệu vượt kênh nhập.                            |

### 1.4. Giá trị biên (BVA)

**LB = 1, biên đóng; Δ = 1 sản phẩm.** Ba điểm BVA được dùng chung với ca EP, không tạo bộ ca trùng.

| BVA-ID    |    Giá trị | Lớp                 | Ca hiện hành |
| --------- | ---------: | ------------------- | ------------ |
| B06-LOW   | 0 = LB − Δ | E06-Q2, không dương | D04          |
| B06-AT    |     1 = LB | E06-Q1, hợp lệ      | D02          |
| B06-ABOVE | 2 = LB + Δ | E06-Q1, hợp lệ      | D03          |

SRS không quy định UB nên bộ ca chỉ phân tích cận dưới có căn cứ. Giá trị -1 là đại diện bổ sung của Q2; 1.5 và abc kiểm tra điều kiện kiểu/miền; ô rỗng kiểm tra thao tác xóa nội dung. Các ca này không được tính thành điểm BVA mới. Các trường thông tin sản phẩm chỉ được xem, không có thao tác chỉnh sửa để thiết kế biên đầu vào trong FR-06.

### 1.5. Testcase và kết quả

**Tiền điều kiện và tiêu chí đánh giá:**

**PC:** P theo fixture; đã đăng nhập; giỏ trống; trang chi tiết P tải xong và chưa bấm Thêm trong lượt mở trang đó. Mỗi ca thêm độc lập. Bảng testcase đánh giá một lần bấm; quan sát bấm tiếp lần thứ hai được ghi riêng trong ghi chú thực thi bên dưới.

**R06-03:** Sau một lần bấm, có toast notification hoặc badge cập nhật là đạt mục tiêu phản hồi; không có toast và cũng không có badge cập nhật là không đạt. Tiêu chí áp dụng cho D02–D08 theo câu chữ “sau khi bấm” của FR-06. Chỉ cần một trong hai hình thức, không bắt buộc đồng thời có cả hai.

**R06-02:** Với dữ liệu hợp lệ, đối chiếu sản phẩm và số lượng được thêm. Với dữ liệu không hợp lệ hoặc rỗng, không ghi số lượng ngoài miền nguyên dương vào giỏ. Giỏ trống sau một lần bấm không tự chứng minh cơ chế kiểm tra số lượng hoạt động đúng, vì D02/D03 với số lượng hợp lệ cũng không thêm được. Với D08, không yêu cầu tự điền mặc định hoặc một thông báo lỗi cụ thể.

**Bảng testcase:**

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

**Ghi chú thực thi:**

Các quan sát sau được ghi nhận khi bấm tiếp lần thứ hai trên cùng trang; chúng được tách khỏi kết quả một lần bấm trong bảng testcase.

- **F06-D04 — số lượng `0`:** Nhập `0`, ấn hai lần thì vẫn thêm được sản phẩm vào giỏ với số lượng `0`. [Ảnh giỏ có số lượng 0](screenshots/FR06/D04-3.png).
- **F06-D05 — số lượng `-1`:** Nhập `-1`, ấn hai lần thì vẫn thêm được sản phẩm vào giỏ với số lượng `-1`. [Ảnh giỏ có số lượng -1](screenshots/FR06/D08-3.png).
- **F06-D08 — ô rỗng:** Xóa ô nhập cho rỗng, ấn hai lần thì số lượng, thành tiền và tổng tiền trong giỏ hiện `NaN`. [Ảnh giỏ hiển thị NaN](screenshots/FR06/D08-3.png).

### 1.6. Truy vết và kết luận

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

## 2. FR-07 — Giỏ hàng

### 2.1. Yêu cầu và dữ liệu

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-07. Phương pháp: slide “S04 — Domain Testing”. Các mã R07 dùng để truy vết trong báo cáo.

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

**Dữ liệu kiểm thử:**

| Ký hiệu | ID trên seed sạch | Tên sản phẩm | Đơn giá theo seed | Danh mục |
|---|---|---|---:|---|
| P | 1 | iPhone 15 Pro Max | 30000000 ₫ | Điện thoại |
| Q | 2 | Samsung Galaxy S24 Ultra | 28000000 ₫ | Điện thoại |

**Tiền điều kiện chung:** Web dùng baseline nêu tại mục 1.1; phiên đã đăng nhập bằng tài khoản `test@eshop.com`. Giỏ và số lượng dưới đây là dữ liệu cần chuẩn bị cho từng đại diện, không phải dữ liệu giỏ có sẵn trong seed hoặc trạng thái đã được xác minh. Chọn P có số lượng 3 và Q có số lượng 2 làm mẫu cụ thể để dễ đối chiếu hai dòng khác nhau; các số này không xác lập miền hợp lệ, min/max hoặc điểm BVA của FR-07.

### 2.2. Phân lớp (EP)

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-07. Phương pháp: slide “S04 — Domain Testing”. Các lớp được xác định theo input/output và trạng thái ở mục 2.1; hai tình huống cùng lớp có cùng cách xử lý hoặc cùng loại kết quả kỳ vọng, không nhất thiết có cùng giá trị số lượng hay số tiền.

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

### 2.3. Đại diện

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-07. Nguồn sản phẩm: file `backend/database.js`, cùng baseline seed với FR-06. Phương pháp: slide “S04 — Domain Testing”, chọn đại diện và kết hợp các lớp hợp lệ.

| REP-ID | Dữ liệu và thao tác đại diện | Lớp được phủ | Kỳ vọng theo yêu cầu | Lý do chọn / giới hạn |
|---|---|---|---|---|
| REP07-EMPTY | Giỏ trống, n = 0; mở giỏ để đối chiếu đầu ra trống. | E07-C0, E07-OEMPTY | Hình minh họa và thông báo giỏ trống rõ ràng (R07-06). | Gộp EP giỏ trống với điểm B07-EMPTY trong F07-D01. Điều hướng được đánh giá riêng bằng giỏ có hàng. |
| REP07-CART | Giỏ chỉ có P:3 hoặc có P:3, Q:2. Trạng thái hai dòng được chuẩn bị với Q chưa có trong giỏ trước khi thêm. Kiểm tra danh sách/nhãn; mỗi thao tác + hoặc − bắt đầu riêng từ giỏ P:3, Q:2. | E07-P2 (chuẩn bị), E07-C1, E07-OLIST, E07-OTOTAL; E07-QPLUS, E07-OINCREASE; E07-QMINUS, E07-ODECREASE | Đủ năm cột và nút +/−; dữ liệu tương ứng giỏ; nhãn “Tổng cộng”; chỉnh số lượng theo hướng tăng/giảm (R07-01/05). | Dùng chung dữ liệu cho F07-D02–D06, giữ kết quả riêng cho hiển thị, nhãn, tăng và giảm. Hai trạng thái một/hai dòng dùng chung với BVA. |
| REP07-DUP | Tiền điều kiện: giỏ đã có P:3, Q:2. Thêm lại cùng sản phẩm P, sau đó đối chiếu số lượng và số dòng của P trong giỏ. | E07-P1, E07-ODUPLICATE | Số lượng P tăng so với 3; P vẫn chỉ có một dòng (R07-02). | Cùng ID P đại diện thêm trùng; Q phân biệt với sản phẩm khác. Dùng F07-D07; không tự đặt lượng cộng chính xác. |
| REP07-DELETE | Giỏ P:3, Q:2; dialog chưa mở. Bấm Xóa trên dòng P và quan sát trước khi quyết định xác nhận. | E07-DEL, E07-D0, E07-D1, E07-ODELETE | Dialog xác nhận xuất hiện trước khi xóa P (R07-03). | Gộp hai trạng thái dialog và đầu ra của cùng yêu cầu xóa trong F07-D08. |
| REP07-NAV | Giỏ P:3, Q:2; bấm Tiếp tục mua sắm. | E07-NAV, E07-ONAV | Hiển thị trang chủ (R07-04). | F07-D09 đánh giá điều hướng riêng, không phụ thuộc kết quả giỏ trống. |

Năm nhóm đại diện được tổng hợp thành bảng testcase ở mục 2.5. Các lớp đầu ra dùng chung ca với input/trạng thái tạo ra chúng; E07-P2 là điều kiện chuẩn bị dữ liệu, không có ca đánh giá riêng luồng thêm mới trong FR-07.

### 2.4. Giá trị biên (BVA)

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-07. Phương pháp: slide “S04 — Domain Testing”. Dữ liệu P/Q và tiền điều kiện đăng nhập dùng lại mục 2.1.

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
| Số lượng trên mỗi dòng, thao tác +/− và lượng cộng khi thêm trùng | Chưa đủ căn cứ đặt biên số lượng: FR-07 chưa quy định min/max, bước tăng/giảm hoặc xử lý tại 0. Giữ các đại diện EP ở mục 2.3; không lấy min 1 của FR-06 làm biên của FR-07. |
| Sản phẩm đã có/chưa có trong giỏ | Quan hệ định danh không có thứ tự số để áp BVA. Kiểm tra gộp dòng bằng REP07-DUP theo R07-02; ID và giá của P/Q không trở thành biên. |
| Yêu cầu Xóa, trạng thái dialog và Tiếp tục mua sắm | Các thao tác/trạng thái được phân biệt theo loại và trình tự, không phải khoảng số có cận. Giữ kiểm tra EP tương ứng, không gán số để tạo BVA. |
| Nhãn tổng tiền, thành tiền và tổng tiền | Nhãn là nội dung cần đối chiếu theo R07-05, không có biên độ dài được quy định. FR-07 chưa nêu công thức, giới hạn tiền hoặc quy tắc làm tròn để thiết kế biên tiền; chưa đặt giá trị tổng cụ thể. |

### 2.5. Testcase và kết quả

**Tiền điều kiện và tiêu chí đánh giá:**

**PC chung:** Web và dữ liệu P/Q theo mục 2.1; đã đăng nhập bằng tài khoản `test@eshop.com`; trang giỏ tải xong. Chuẩn bị đúng trạng thái trước mỗi ca; không dùng kết quả của ca trước làm tiền điều kiện cho ca sau.

| Mã PC | Trạng thái giỏ trước ca |
|---|---|
| PC0 | Giỏ trống, n = 0. |
| PC1 | Giỏ đã có đúng một dòng P (ID 1, iPhone 15 Pro Max), đơn giá 30000000 ₫, số lượng 3; n = 1. |
| PC2 | Giỏ đã có đúng hai dòng: P như PC1 và Q (ID 2, Samsung Galaxy S24 Ultra), đơn giá 28000000 ₫, số lượng 2; n = 2. |

PC1/PC2 là tiền điều kiện **giỏ đã có hàng**, không phải kết quả cần tạo trong các bước đánh giá hiển thị, chỉnh số lượng, thêm trùng, xóa hoặc điều hướng. E07-P2 chỉ nằm ở khâu chuẩn bị PC2 từ giỏ có P nhưng chưa có Q; không đặt oracle riêng cho thao tác thêm sản phẩm mới.

**Tiêu chí:** So sánh số lượng sau +/− với giá trị trước thao tác, không mặc định bước ±1 hoặc giới hạn số lượng. Thêm trùng đánh giá đồng thời tăng số lượng và giữ một dòng cho P theo R07-02, không đặt kết quả phải bằng 5. Thành tiền/tổng tiền không có kỳ vọng số cụ thể; nhãn được đánh giá riêng. Ca xóa chỉ đánh giá dialog xuất hiện trước khi xóa, không tự đặt nội dung dialog hoặc kết quả hủy/đóng. Với giỏ trống, không yêu cầu nguyên văn thông báo hay ảnh cụ thể.

**Bảng testcase:**

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

### 2.6. Truy vết và kết luận

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

Giới hạn thiết kế giữ theo mục 2.1 và 2.4: không đặt min/max số lượng, bước tăng/giảm, giới hạn tồn kho, công thức tiền hoặc hành vi hủy/đóng dialog ngoài đặc tả.

## 3. FR-14 — Danh mục Admin

### 3.1. Yêu cầu và dữ liệu

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-14. Phương pháp: slide “S04 — Domain Testing”. Các mã R14 dùng để truy vết trong báo cáo.

| Mã | Yêu cầu FR-14 |
|---|---|
| R14-01 | Admin có thể thêm danh mục. |
| R14-02 | Admin có thể xem danh mục. |
| R14-03 | Admin có thể xóa danh mục. |
| R14-04 | Tên danh mục là bắt buộc, không được để trống. |

Phạm vi là quản lý danh mục trên Web Admin, gồm Thêm/Xem/Xóa và ràng buộc tên. Phiên Admin là tiền điều kiện; kiểm tra phân quyền riêng thuộc FR-12.

| ID | Loại / biến | Ràng buộc hoặc kỳ vọng |
|---|---|---|
| I14-A | Input: thao tác Thêm / Xem / Xóa | Chọn thao tác quản lý danh mục theo R14-01/02/03. |
| I14-N | Input: tên danh mục khi thêm | Chuỗi ký tự; bắt buộc, không được để trống theo R14-04. |
| I14-C | Input: danh mục mục tiêu khi xóa | Chọn một danh mục đang tồn tại; dùng định danh và tên để xác định mục tiêu. |
| S14-SESSION | Trạng thái: phiên và vai trò | Đã đăng nhập bằng tài khoản có vai trò Admin trước khi thao tác FR-14. |
| S14-CATEGORIES | Trạng thái / dữ liệu danh mục | Danh sách danh mục hiện có với định danh và tên; làm dữ liệu đối chiếu trước/sau thêm hoặc xóa. |
| S14-REFERENCE | Trạng thái: sản phẩm tham chiếu danh mục | Ghi nhận danh mục mục tiêu có đang được sản phẩm tham chiếu hay không. |
| O14-LIST | Output: danh mục được hiển thị | Admin xem được các danh mục tương ứng dữ liệu đã chuẩn bị theo R14-02. |
| O14-CREATE | Output: danh mục sau thao tác thêm | Thêm được danh mục với tên đáp ứng ràng buộc đã nêu; danh sách phản ánh danh mục được thêm theo R14-01/02. |
| O14-DELETE | Output: danh mục sau thao tác xóa | Danh mục mục tiêu không còn trong danh sách sau khi thực hiện xóa theo R14-03, trong nhánh xóa đã giới hạn bên dưới. |
| O14-VALIDATION | Output: kiểm soát tên rỗng | Không tạo danh mục có tên rỗng theo R14-04. |

**Giới hạn đối chiếu dùng chung:**

- Chỉ xét điều kiện tên bắt buộc. Không thiết kế tình huống tên trùng, phân biệt hoa/thường hoặc dấu, chuẩn hóa khoảng trắng hay tên chỉ gồm khoảng trắng vì FR-14 chưa quy định cách xử lý. Không suy ra tên phải duy nhất, không đặt độ dài tối đa hoặc lấy giới hạn tên sản phẩm của FR-15 áp cho tên danh mục.
- Nhánh xóa chỉ dùng danh mục thử đang tồn tại, không có sản phẩm tham chiếu; không đặt kỳ vọng cascade, từ chối xóa hoặc xử lý sản phẩm cho danh mục đang được tham chiếu. Không khảo sát ID sai kiểu/không tồn tại qua API hoặc thao tác Sửa dù tiêu đề FR-14 dùng “Category CRUD”.
- Không đặt tên nút, cách mở biểu mẫu, dialog xác nhận, lựa chọn hủy/đóng, nguyên văn thông báo hoặc hình thức phản hồi ngoài đặc tả. Khi Xem, không yêu cầu cột ID, thứ tự sắp xếp, phân trang, hình minh họa hay thông báo danh sách trống; ID dùng để nhận diện dữ liệu, không phải giá trị bắt buộc nhập trên Web.
- Giữ nguyên ba danh mục seed làm dữ liệu nền; chỉ thêm/xóa danh mục thử để đối chiếu các thao tác trong bộ ca.

**Dữ liệu nền:** Lấy từ file `backend/database.js`, cùng baseline SUT ở mục 1.1. Dữ liệu seed là căn cứ chuẩn bị và đối chiếu, không phải kết quả quan sát mới; danh sách đang chạy cần được đối chiếu với dữ liệu nền thực tế.

| Ký hiệu dữ liệu | ID trên seed sạch | Tên danh mục | Sản phẩm tham chiếu trong seed |
|---|---|---|---|
| C14-DT | 1 | Điện thoại | iPhone 15 Pro Max; Samsung Galaxy S24 Ultra |
| C14-LT | 2 | Laptop | MacBook Pro M3 |
| C14-PK | 3 | Phụ kiện | Tai nghe AirPods Pro 2; Bàn phím cơ Keychron Q1 |

**Tiền điều kiện và dữ liệu thử:** Web Admin truy cập được; đăng nhập bằng tài khoản seed `admin@eshop.com`, vai trò `admin`; trang quản lý danh mục tải xong. Danh sách nền và trạng thái tham chiếu của mục tiêu phải được xác định trước thao tác. Ba danh mục seed đều có sản phẩm tham chiếu, chỉ dùng đối chiếu hiển thị. Tên và mục tiêu thử ở mục 3.3 là dữ liệu thử được lựa chọn khi thiết kế, không thuộc danh mục seed; kết quả thực thi được ghi ở mục 3.5. Chọn tên chưa có để cô lập dữ liệu, không phải quy tắc tên phải duy nhất. C14-DEL được chuẩn bị riêng trước ca xóa, không lấy kết quả ca thêm C14-ADD làm tiền điều kiện; ghi nhận định danh thực tế khi chuẩn bị, không suy ra ID kế tiếp từ seed.

### 3.2. Phân lớp (EP)

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-14; đối chiếu file `references/SRS.md`, cùng mục FR-14. Phương pháp: slide “S04 — Domain Testing”. Các lớp dùng input/output, trạng thái và giới hạn đối chiếu ở mục 3.1.

Phiên Admin là tiền điều kiện chung. Phân lớp riêng theo tên khi Thêm, dữ liệu danh sách khi Xem và mục tiêu khi Xóa; các biến khác nhau không tạo một phép phân hoạch loại trừ nhau cho toàn bộ chức năng.

| EC-ID | Biến / điều kiện | Miền/lớp | Phân loại và kỳ vọng |
|---|---|---|---|
| E14-N1 | I14-N, chọn Thêm theo I14-A | Tên không rỗng, chứa ít nhất một ký tự khác khoảng trắng; trong phạm vi tên ở mục 3.1 | **Đầu vào hợp lệ theo điều kiện tên bắt buộc** của R14-04; thêm được danh mục và danh sách phản ánh danh mục được thêm theo R14-01/02. |
| E14-N0 | I14-N, cùng thao tác Thêm | Ô tên không có ký tự nào, do chưa nhập hoặc đã xóa hết nội dung | **Đầu vào không hợp lệ** do vi phạm R14-04; không tạo danh mục có tên rỗng. |
| E14-C1 | S14-CATEGORIES, chọn Xem theo I14-A | Danh sách danh mục theo fixture hợp lệ đã chuẩn bị; không đặt giới hạn số danh mục | **Trạng thái hợp lệ**; Admin xem được các danh mục tương ứng dữ liệu nền đã chuẩn bị theo R14-02. |
| E14-D0 | I14-C và S14-REFERENCE, chọn Xóa theo I14-A | Danh mục mục tiêu đang tồn tại và không có sản phẩm tham chiếu trước thao tác | **Đầu vào/trạng thái hợp lệ cho nhánh xóa chính**; xóa được đúng mục tiêu, danh mục đó không còn trong danh sách theo R14-03/02. |

N1/N0 rời nhau trong miền tên đã giới hạn ở mục 3.1. Khi Xem, đối chiếu danh sách với fixture đã chuẩn bị theo R14-02; không đặt hành vi hiển thị riêng hoặc min/max số danh mục. D0 thuộc nhánh xóa chính đã xác định.

| EC-ID | Biến / điều kiện | Lớp đầu ra kỳ vọng |
|---|---|---|
| E14-OCREATE | O14-CREATE và O14-LIST, Thêm với tên thuộc N1 | Thêm được danh mục; danh sách phản ánh tên tương ứng dữ liệu chuẩn bị theo R14-01/02/04. |
| E14-OVALIDATION | O14-VALIDATION, Thêm với tên thuộc N0 | Không tạo danh mục có tên rỗng theo R14-04; đây là đầu ra đúng kỳ vọng của đầu vào invalid. |
| E14-OLIST | O14-LIST, Xem với trạng thái C1 | Các danh mục hiển thị tương ứng dữ liệu nền đã chuẩn bị theo R14-02. |
| E14-ODELETE | O14-DELETE và O14-LIST, Xóa với mục tiêu thuộc D0 | Danh mục mục tiêu không còn trong danh sách sau khi xóa theo R14-03/02. |

### 3.3. Đại diện

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-14. Phương pháp: slide “S04 — Domain Testing”, chọn đại diện và kết hợp các lớp hợp lệ. Mã lớp theo mục 3.2; tiền điều kiện và giới hạn dùng chung ở mục 3.1.

| Ký hiệu | Tên cụ thể | Trạng thái cần chuẩn bị |
|---|---|---|
| C14-ADD | `Danh mục thử thêm FR14 23120226` | Chưa có danh mục mang tên này trước ca thêm để dễ nhận diện dữ liệu mới. |
| C14-DEL | `Danh mục thử xóa FR14 23120226` | Danh mục thử đã tồn tại trước ca xóa và không có sản phẩm tham chiếu. |

| REP-ID | Dữ liệu và thao tác đại diện | Lớp được phủ | Kỳ vọng theo yêu cầu | Lý do chọn / giới hạn |
|---|---|---|---|---|
| REP14-ADDVIEW | Danh sách nền có danh mục; đối chiếu các tên seed ở mục 3.1. Nhập tên C14-ADD, thực hiện Thêm rồi xem lại danh sách. | E14-N1, E14-OCREATE; E14-C1, E14-OLIST | Xem được các danh mục tương ứng dữ liệu nền; thêm được C14-ADD và danh sách phản ánh tên đã nhập (R14-01/02/04). | Gộp Thêm với Xem và đầu ra tương ứng trong một nhóm đại diện. |
| REP14-EMPTYNAME | Danh sách nền có danh mục, phiên Admin hợp lệ. Mở thao tác Thêm, xóa hết nội dung ô tên, giữ chuỗi rỗng `""`, rồi thử Thêm. | E14-N0, E14-OVALIDATION | Không tạo danh mục có tên rỗng (R14-04). | Chỉ vi phạm điều kiện tên bắt buộc, giữ các tiền điều kiện khác hợp lệ. |
| REP14-DELVIEW | Trước ca đã có C14-DEL, xác định đúng tên/định danh và không có sản phẩm tham chiếu. Xem danh sách, chọn đúng C14-DEL để Xóa rồi xem lại danh sách. | E14-D0, E14-ODELETE; E14-C1, E14-OLIST | Xem được C14-DEL trước thao tác; sau khi xóa, mục tiêu không còn trong danh sách (R14-02/03). | Gộp Xóa với Xem trước/sau trên cùng mục tiêu thử được chuẩn bị riêng. |

Tám lớp trong bộ thiết kế được ánh xạ tới REP14-ADDVIEW, REP14-EMPTYNAME và REP14-DELVIEW. Các lớp đầu ra dùng chung thao tác tạo ra chúng; không tạo một testcase cho mỗi dòng phân lớp.

### 3.4. Giá trị biên (BVA)

Nguồn: file `README.md` trong `eshop-sut`, FR-14; slide “S04 — Domain Testing”.

**Biến: độ dài tên I14-N; LB = 1 ký tự, biên đóng; Δ = 1 ký tự**, suy từ yêu cầu tên bắt buộc, không được để trống (R14-04). Giữ phiên Admin, danh sách nền và cách cô lập dữ liệu theo mục 3.1; chỉ thay tên khi Thêm.

| BVA-ID | Dữ liệu / độ dài | Lớp | Ca EP dùng chung | Kỳ vọng |
|---|---|---|---|---|
| B14-LOW | `""`; 0 = LB − Δ | E14-N0, E14-OVALIDATION | REP14-EMPTYNAME, cùng dữ liệu rỗng | Không tạo danh mục có tên rỗng (R14-04). |
| B14-AT | `"A"`; 1 = LB | E14-N1, E14-OCREATE, E14-OLIST | Biến thể REP14-ADDVIEW, thay tên bằng `A` | Thêm được danh mục tên `A`; xem lại danh sách có danh mục tương ứng (R14-01/02/04). |
| B14-ABOVE | `"AB"`; 2 = LB + Δ | E14-N1, E14-OCREATE, E14-OLIST | Biến thể REP14-ADDVIEW, thay tên bằng `AB` | Thêm được danh mục tên `AB`; xem lại danh sách có danh mục tương ứng (R14-01/02/04). |

Ba điểm 0–1–2 ký tự dùng chung luồng và kỳ vọng EP. Khi tổng hợp testcase, `A` và `AB` là hai dữ liệu Thêm/Xem vừa phủ E14-N1 vừa phủ BVA; không tạo ca riêng cho tên mẫu C14-ADD ở mục 3.3 vì không bổ sung mục tiêu cần phủ. Dữ liệu xóa C14-DEL được giữ riêng. SRS không quy định UB nên chỉ phân tích cận dưới có căn cứ; các giới hạn đối chiếu và việc giữ nguyên seed theo mục 3.1.

### 3.5. Testcase và kết quả

**Tiền điều kiện và tiêu chí đánh giá:**

**PC chung:** Web dùng baseline và dữ liệu nền ở mục 3.1; đã đăng nhập bằng tài khoản `admin@eshop.com`, vai trò `admin`; trang quản lý danh mục tải xong. Trước mỗi ca, xác định danh sách nền để đối chiếu khi Xem trước/sau thao tác; giữ nguyên ba danh mục seed. Mỗi ca có tiền điều kiện riêng, không phụ thuộc kết quả ca trước.

| Mã PC | Dữ liệu cần chuẩn bị |
|---|---|
| PC14-NAME | Danh sách có danh mục nền. Trước ca Thêm tên `A` hoặc `AB`, tên tương ứng chưa có để dễ nhận diện dữ liệu mới; đây là cách cô lập dữ liệu, không phải quy tắc cấm trùng tên. |
| PC14-DELETE | Danh sách đã có C14-DEL, tên `Danh mục thử xóa FR14 23120226`; đã xác định đúng mục tiêu và không có sản phẩm tham chiếu. Mục tiêu được chuẩn bị riêng, không lấy dữ liệu sinh ra từ D02/D03 làm tiền điều kiện; không mặc định ID từ seed. |

Chỉ đối chiếu điều kiện tên bắt buộc và kết quả Thêm/Xem/Xóa theo SRS; không yêu cầu nguyên văn thông báo, hình thức phản hồi hoặc dialog.

**Bảng testcase:**

| TC-ID | Mục tiêu / dữ liệu / truy vết | Bước chạy cụ thể | Expected Result | Actual Result | Verdict | Bằng chứng |
|---|---|---|---|---|---|---|
| F14-D01 | Tên rỗng và Xem; PC14-NAME; tên `""`, 0 ký tự; R14-02/04; REP14-EMPTYNAME; E14-N0, E14-OVALIDATION, E14-C1, E14-OLIST; B14-LOW | 1. Xem danh sách, đối chiếu với dữ liệu nền trước ca. 2. Mở thao tác Thêm, xóa hết nội dung ô tên, không nhập ký tự nào. 3. Thử Thêm một lần. 4. Xem lại danh sách, đối chiếu với danh sách trước thao tác. | Xem được các danh mục tương ứng dữ liệu nền trước/sau thao tác; không tạo danh mục có tên rỗng. | Trước thao tác có ba danh mục seed. Ô tên rỗng; bấm Thêm mới một lần tạo thêm danh mục ID 4 không có tên, danh sách tăng từ 3 lên 4 dòng. Ba danh mục seed vẫn hiển thị. | FAIL | [Danh mục tên rỗng được tạo sau một lần bấm](screenshots/FR14/D01.png) |
| F14-D02 | Thêm/Xem tên `A`; PC14-NAME, tên `A` chưa có; 1 ký tự; R14-01/02/04; biến thể REP14-ADDVIEW; E14-N1, E14-OCREATE, E14-C1, E14-OLIST; B14-AT | 1. Xem và đối chiếu danh sách nền, xác định tên `A` chưa có theo tiền điều kiện. 2. Mở thao tác Thêm, nhập `A`, thực hiện Thêm một lần. 3. Xem lại danh sách, đối chiếu tên vừa thêm. | Danh sách trước thao tác phản ánh dữ liệu nền; thêm được danh mục tên `A`, danh sách sau thao tác hiển thị danh mục tương ứng tên đã nhập. | Trước thao tác chưa có danh mục tên A. Nhập A, bấm Thêm mới một lần; danh mục tên A xuất hiện trong danh sách, ba danh mục seed vẫn hiển thị. | PASS | |
| F14-D03 | Thêm/Xem tên `AB`; PC14-NAME, tên `AB` chưa có; 2 ký tự; R14-01/02/04; biến thể REP14-ADDVIEW; E14-N1, E14-OCREATE, E14-C1, E14-OLIST; B14-ABOVE | 1. Xem và đối chiếu danh sách nền, xác định tên `AB` chưa có theo tiền điều kiện. 2. Mở thao tác Thêm, nhập `AB`, thực hiện Thêm một lần. 3. Xem lại danh sách, đối chiếu tên vừa thêm. | Danh sách trước thao tác phản ánh dữ liệu nền; thêm được danh mục tên `AB`, danh sách sau thao tác hiển thị danh mục tương ứng tên đã nhập. | Trước thao tác chưa có danh mục tên AB. Nhập AB, bấm Thêm mới một lần; danh mục tên AB xuất hiện trong danh sách, ba danh mục seed vẫn hiển thị. | PASS | |
| F14-D04 | Xóa/Xem danh mục thử; PC14-DELETE; C14-DEL = `Danh mục thử xóa FR14 23120226`; R14-02/03; REP14-DELVIEW; E14-D0, E14-ODELETE, E14-C1, E14-OLIST | 1. Xem danh sách, đối chiếu C14-DEL với tên/định danh mục tiêu đã chuẩn bị; bảo đảm điều kiện không có sản phẩm tham chiếu. 2. Thực hiện Xóa đúng C14-DEL. 3. Xem lại danh sách, đối chiếu mục tiêu với trước thao tác. | Xem được C14-DEL trong danh sách trước thao tác; xóa được danh mục thử, mục tiêu không còn trong danh sách sau thao tác. Danh sách vẫn có các danh mục seed. | Danh mục thử xóa FR14 23120226 được chuẩn bị riêng và hiển thị trước thao tác. Bấm Xóa tại đúng dòng mục tiêu một lần; danh mục thử biến mất khỏi danh sách, ba danh mục seed vẫn hiển thị. | PASS | |

### 3.6. Truy vết và kết luận

**Truy vết thiết kế:**

| Yêu cầu / lớp / biên | Ca hiện hành / phạm vi triển khai |
|---|---|
| R14-04: E14-N0, E14-OVALIDATION; REP14-EMPTYNAME; B14-LOW | F14-D01 |
| R14-01/04: E14-N1, E14-OCREATE; biến thể REP14-ADDVIEW; B14-AT, B14-ABOVE | F14-D02, F14-D03 |
| R14-02: E14-C1, E14-OLIST; Xem trước/sau thao tác | F14-D01–D04 |
| R14-03: E14-D0, E14-ODELETE; REP14-DELVIEW | F14-D04 |
| B14-LOW / B14-AT / B14-ABOVE: độ dài tên 0–1–2 ký tự | F14-D01 / F14-D02 / F14-D03 |

Bộ thiết kế gồm **4 ca F14-D01–D04 = 1 ca tên rỗng + 2 ca Thêm/Xem tại các điểm BVA hợp lệ + 1 ca Xóa/Xem danh mục thử**. Ba điểm BVA dùng chung ca EP; không giữ ca riêng cho tên mẫu dài cùng lớp E14-N1. Kiểm tra Xem được kết hợp trước/sau mỗi thao tác, không tạo ca Xem riêng.

Bốn ca đã thực thi ánh xạ tới tám lớp trong bộ thiết kế ở mục 3.2 và ba điểm BVA 0–1–2 ký tự ở mục 3.4. Phạm vi kết luận giới hạn ở dữ liệu và các ca đã thực hiện.

**Kết quả thực thi:** Đã chạy 4 ca F14-D01–D04, gồm **3 PASS và 1 FAIL**. F14-D01 không đáp ứng R14-04 vì tên rỗng vẫn tạo được danh mục; bằng chứng được liên kết trong bảng testcase. F14-D02–D04 đáp ứng kỳ vọng của các ca tương ứng trong phạm vi đã kiểm tra.

## 4. FR-20 — Hồ sơ Mobile

### 4.1. Yêu cầu và dữ liệu

Nguồn: file `README.md` trong `eshop-sut`, FR-04 và phần Hồ sơ của FR-20. Phương pháp: slide “S04 — Domain Testing”, xác định input/output. FR-20 xác định chức năng Hồ sơ trên Mobile; các quy tắc hồ sơ ở FR-04 được dùng để đối chiếu trong phạm vi này.

| Mã | Yêu cầu áp dụng cho Hồ sơ Mobile | Nguồn |
|---|---|---|
| R20-01 | Mobile có chức năng Hồ sơ. | FR-20 |
| R20-02 | Người dùng đã đăng nhập có thể cập nhật Họ Tên, Số điện thoại và Địa chỉ giao hàng mặc định. | FR-04 |
| R20-03 | Số điện thoại hợp lệ bắt đầu bằng `0`, có từ 10 đến 11 chữ số. | FR-04 |
| R20-04 | Email không được phép thay đổi qua giao diện. | FR-04 |
| R20-05 | Người dùng chỉ được cập nhật hồ sơ của chính mình. | FR-04 |
| R20-06 | Người dùng không thể tự thay đổi thuộc tính `role`. | FR-04 |

Phạm vi là mở Hồ sơ và cập nhật các trường được phép trên Mobile, kiểm soát số điện thoại và bảo vệ Email, role, hồ sơ người khác. Các chức năng Mobile khác, đăng nhập/đăng ký, hủy đơn hàng và yêu cầu GUI riêng không thuộc phần này; phiên đăng nhập là tiền điều kiện.

| ID | Loại / biến | Ràng buộc hoặc kỳ vọng |
|---|---|---|
| I20-NAME | Input: Họ Tên mới | Chuỗi ký tự được phép cập nhật theo R20-02; SRS chưa nêu bắt buộc, độ dài hoặc tập ký tự. |
| I20-PHONE | Input: Số điện thoại mới | Chuỗi chữ số để giữ ký tự `0` đầu; định dạng hợp lệ gồm toàn chữ số, bắt đầu bằng `0`, dài 10–11 chữ số theo R20-03. Không coi đây là một số để bỏ số 0 đầu hoặc tính toán. |
| I20-ADDRESS | Input: Địa chỉ giao hàng mặc định mới | Chuỗi ký tự được phép cập nhật theo R20-02; chưa nêu bắt buộc, độ dài hoặc cấu trúc địa chỉ. |
| I20-A | Input: mở Hồ sơ / yêu cầu lưu cập nhật | Mở chức năng theo R20-01; lưu các thay đổi được phép của người dùng đã đăng nhập theo R20-02/05. Tên nút và thao tác điều hướng trong bước chạy theo giao diện hiện hành, không phải ràng buộc bổ sung của SRS. |
| I20-TARGET | Input/điều kiện: hồ sơ được yêu cầu cập nhật | Đối chiếu định danh mục tiêu với người dùng trong phiên; chỉ cập nhật hồ sơ của chính mình theo R20-05. Đây là biến phân tích quyền, không khẳng định Mobile có ô nhập ID. |
| I20-EMAIL | Input/điều kiện: yêu cầu thay đổi Email qua giao diện | Không được thay đổi Email theo R20-04; không dùng quy tắc định dạng Email đăng ký để mở rộng phần Hồ sơ. |
| I20-ROLE | Input/điều kiện: yêu cầu tự đổi role | Không được tự đổi role theo R20-06; chưa khẳng định giao diện có trường hoặc luồng chỉnh role. |
| S20-SESSION | Trạng thái: người dùng đã đăng nhập | Gọi người dùng hiện tại là U; xác định U từ phiên, không từ một giá trị người dùng tự thay thế. |
| S20-PROFILE | Trạng thái / dữ liệu hồ sơ ban đầu | Hồ sơ chuẩn của U trước thao tác gồm tên, số điện thoại, địa chỉ, Email và role để đối chiếu thay đổi. Nếu xét yêu cầu tác động đến người khác, cần hồ sơ của tài khoản thử V khác U làm dữ liệu nền. |
| O20-VIEW | Output: Hồ sơ trên Mobile | Truy cập được chức năng Hồ sơ; thông tin dùng để xem/cập nhật tương ứng tài khoản U theo R20-01/02/05. Không yêu cầu role phải được hiển thị trên màn hình. |
| O20-UPDATE | Output: dữ liệu được cập nhật | Các trường được phép của U phản ánh nội dung đã lưu với dữ liệu hợp lệ theo R20-02/03/05. |
| O20-PHONE | Output: kiểm soát số điện thoại | Không lưu số điện thoại vi phạm R20-03; SRS chưa quy định cơ chế chặn hoặc nguyên văn phản hồi. |
| O20-PROTECTED | Output: Email, role và phạm vi tác động | Email không bị thay đổi qua giao diện, role không bị người dùng tự thay đổi, hồ sơ người khác không bị cập nhật bởi U theo R20-04/05/06. |

**Tiền điều kiện và dữ liệu nền:** Ứng dụng Mobile đúng bản SUT có thể truy cập hệ thống; U đã đăng nhập và có hồ sơ được xác định từ dữ liệu chuẩn trước khi cập nhật. Giữ các trường không phải mục tiêu ở dữ liệu nền đáp ứng ràng buộc đã biết; dùng tài khoản thử do người kiểm thử kiểm soát khi cần V khác U. Giá trị hồ sơ ban đầu cần được xác nhận khi chuẩn bị từng đại diện; dữ liệu nền không phải kết quả thực thi.

**Giới hạn đối chiếu dùng chung:**

- Không đặt độ dài tối đa, điều kiện bắt buộc, trim, tập ký tự cho tên/địa chỉ hoặc cách chuẩn hóa số điện thoại ngoài SRS. Chưa đủ căn cứ kết luận việc để rỗng số điện thoại là được phép bỏ trống, giữ giá trị cũ hay phải bị từ chối.
- Chỉ đối chiếu số điện thoại theo ba điều kiện đã nêu; không thêm quy tắc nhà mạng, đầu số chi tiết hoặc giới hạn chỉ 10 chữ số.
- Không yêu cầu một kiểu thông báo, toast, thời gian phản hồi hay cơ chế hoàn tác. Khi số điện thoại sai, chỉ đặt kỳ vọng không lưu số sai; SRS chưa nói việc lưu các trường khác trong cùng yêu cầu có phải thành công/thất bại đồng thời hay không.
- Email được kiểm tra qua cơ chế khóa chỉnh sửa trên UI. Chưa tìm thấy đường tự đổi role hoặc chọn hồ sơ người khác trong phạm vi Hồ sơ Mobile đã quan sát; không tự tạo ô nhập hoặc chuyển sang API để nhận hai ca này là đã thực thi.

### 4.2. Phân lớp (EP)

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, FR-04 và FR-20. Phương pháp: slide “S04 — Domain Testing”. Phân lớp theo từng biến/điều kiện ở mục 4.1; các biến khác nhau có thể kết hợp trong cùng tình huống. Phiên đã đăng nhập được giữ làm tiền điều kiện chung.

Với số điện thoại không rỗng, xét theo thứ tự **ký tự → ký tự đầu → độ dài**: chuỗi có ký tự không phải chữ số thuộc P-TYPE; nếu toàn chữ số thì tách sai/đúng ký tự đầu; chỉ khi bắt đầu bằng `0` mới tách ba khoảng độ dài. Nhờ đó các lớp điện thoại dưới đây rời nhau và phủ miền chuỗi không rỗng. Trường hợp rỗng được ghi riêng ở phần chưa đủ căn cứ.

| EC-ID | Biến / điều kiện | Miền/lớp | Phân loại và kỳ vọng |
|---|---|---|---|
| E20-SELF | I20-TARGET, S20-SESSION | Hồ sơ mục tiêu thuộc U đang đăng nhập | **Trạng thái hợp lệ**; truy cập Hồ sơ của U và được cập nhật các trường cho phép với dữ liệu hợp lệ (R20-01/02/05). |
| E20-OTHER | I20-TARGET, cùng điều kiện chủ sở hữu | Yêu cầu cập nhật hồ sơ của V khác U | **Yêu cầu không hợp lệ về quyền**; không cập nhật hồ sơ V bởi U (R20-05). Khả năng đưa yêu cầu này qua Mobile theo giới hạn mục 4.1. |
| E20-NAME | I20-NAME, thao tác cập nhật của U | Thay Họ Tên bằng chuỗi có nội dung, không rỗng và không chỉ gồm khoảng trắng | **Thao tác được phép trong phạm vi đối chiếu**; lưu tên mới cho U khi các dữ liệu đi kèm đáp ứng ràng buộc đã biết (R20-02/05). Không suy ra tên là trường bắt buộc. |
| E20-ADDRESS | I20-ADDRESS, thao tác cập nhật của U | Thay Địa chỉ giao hàng mặc định bằng chuỗi có nội dung, không rỗng và không chỉ gồm khoảng trắng | **Thao tác được phép trong phạm vi đối chiếu**; lưu địa chỉ mới cho U khi dữ liệu đi kèm đáp ứng ràng buộc đã biết (R20-02/05). Không suy ra địa chỉ là trường bắt buộc. |
| E20-P-TYPE | I20-PHONE, chuỗi không rỗng | Có ít nhất một ký tự không phải chữ số, không phụ thuộc ký tự đầu hoặc độ dài | **Không hợp lệ về định dạng**; không lưu chính số điện thoại này (R20-03). |
| E20-P-PREFIX | I20-PHONE, chuỗi không rỗng gồm toàn chữ số | Ký tự đầu khác `0`, không phụ thuộc độ dài | **Không hợp lệ về ký tự đầu**; không lưu chính số điện thoại này (R20-03). |
| E20-P-SHORT | I20-PHONE, toàn chữ số, bắt đầu bằng `0` | Độ dài từ 1 đến dưới 10 chữ số | **Không hợp lệ về độ dài**; không lưu chính số điện thoại này (R20-03). |
| E20-P-VALID | I20-PHONE, toàn chữ số, bắt đầu bằng `0` | Độ dài từ 10 đến 11 chữ số | **Đầu vào hợp lệ**; được cập nhật số điện thoại của U khi dữ liệu đi kèm hợp lệ (R20-02/03/05). Hai độ dài cùng loại xử lý nên dùng chung một lớp EP. |
| E20-P-LONG | I20-PHONE, toàn chữ số, bắt đầu bằng `0` | Độ dài trên 11 chữ số | **Không hợp lệ về độ dài**; không lưu chính số điện thoại này (R20-03). |
| E20-EMAIL | I20-EMAIL, thao tác qua giao diện | Thử đổi Email của U sang giá trị khác Email ban đầu qua giao diện | **Thao tác thay đổi không được phép**; Email không bị thay đổi qua giao diện (R20-04). Cơ chế khóa nhập được đánh giá ngay tại UI, không yêu cầu gửi được yêu cầu sửa Email. |
| E20-ROLE | I20-ROLE, thao tác của U | Yêu cầu tự đổi role sang giá trị khác role ban đầu | **Yêu cầu thay đổi không được phép**; role không bị U tự thay đổi (R20-06). Không giả định có ô chọn role trên Mobile. |

Các lớp tên/địa chỉ xác định thao tác chỉnh sửa có nội dung, không phân hoạch đầy đủ các dạng chuỗi chưa có quy định. Với điện thoại, P-TYPE hoặc P-PREFIX có thể chứa dữ liệu vi phạm thêm điều kiện khác; các đại diện invalid ở mục 4.3 chỉ vi phạm điều kiện mục tiêu, giữ các điều kiện còn lại đúng để tránh che lỗi.

| EC-ID | Biến / điều kiện | Lớp đầu ra kỳ vọng |
|---|---|---|
| E20-O-VIEW | O20-VIEW, mở Hồ sơ của U | Truy cập được Hồ sơ trên Mobile; dữ liệu dùng để xem/cập nhật tương ứng hồ sơ chuẩn của U (R20-01/02/05). |
| E20-O-UPDATE | O20-UPDATE, cập nhật trường được phép với dữ liệu hợp lệ và mục tiêu SELF | Tên, số điện thoại hoặc địa chỉ được cập nhật tương ứng dữ liệu đã lưu cho U (R20-02/03/05). |
| E20-O-PHONE-REJECT | O20-PHONE, điện thoại thuộc P-TYPE/P-PREFIX/P-SHORT/P-LONG | Không lưu số điện thoại sai định dạng; đây là đầu ra đúng kỳ vọng của input invalid (R20-03). Không suy ra toàn bộ các trường khác phải được hoàn tác. |
| E20-O-EMAIL-KEEP | O20-PROTECTED, cập nhật hồ sơ hoặc yêu cầu đổi Email qua UI | Email của U giữ giá trị được bảo vệ, không bị thay đổi qua giao diện (R20-04). |
| E20-O-ROLE-KEEP | O20-PROTECTED, cập nhật hồ sơ hoặc yêu cầu tự đổi role | Role của U không bị tự thay đổi (R20-06). |
| E20-O-OTHER-PROTECTED | O20-PROTECTED, yêu cầu cập nhật với mục tiêu OTHER | Hồ sơ V không bị cập nhật bởi yêu cầu của U (R20-05). |

**Phần chưa đủ căn cứ phân lớp:**

| Biến / tình huống | Giới hạn |
|---|---|
| Số điện thoại rỗng | Chưa rõ trường có bắt buộc hay được bỏ trống; chưa kết luận valid/invalid hoặc đặt kỳ vọng giữ cũ/từ chối. Không gộp vào P-SHORT vì lớp đó chỉ xét chuỗi chữ số không rỗng. |
| Tên/địa chỉ rỗng, chỉ gồm khoảng trắng, chuẩn hóa hoặc giới hạn độ dài | Chưa có quy tắc trong FR-04/FR-20 để tách lớp với kỳ vọng chấp nhận/từ chối cụ thể. |
| Đường nhập cho mục tiêu OTHER hoặc role | Quy tắc bảo vệ có oracle, nhưng chưa tìm thấy luồng tạo yêu cầu và nguồn quan sát tương ứng trong Hồ sơ Mobile. Không kết luận PASS chỉ từ việc không thấy ô nhập. Email được đánh giá riêng bằng D07. |

### 4.3. Đại diện

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, FR-04/FR-20. Phương pháp: slide “S04 — Domain Testing”, chọn đại diện và kết hợp các lớp hợp lệ. Mã lớp theo mục 4.2.

**Dữ liệu và tiền điều kiện:** U dùng tài khoản thử `test@eshop.com`, role `user`; V là tài khoản thử khác U, `admin@eshop.com`, theo file seed `backend/database.js`. Cần đối chiếu đúng tài khoản trên baseline Mobile trước khi dùng; không suy ra ID hoặc hồ sơ đang chạy chỉ từ tên tài khoản. Gọi hồ sơ đã lưu trước từng ca của U là N0/P0/A0/Email0/Role0. P0 có thể rỗng trên UI hoặc null trong dữ liệu nền; không yêu cầu P0 hợp lệ vì mỗi ca điện thoại thay giá trị mục tiêu trước khi lưu. Tên và địa chỉ mới dưới đây là dữ liệu thử; kết quả lưu được ghi ở mục 4.5.

| Ký hiệu | Dữ liệu cụ thể / điều kiện |
|---|---|
| N1 | `Nguyễn Văn Kiểm Thử`, dùng với hồ sơ có N0 khác N1 để nhận diện thay đổi. |
| A1 | `20 Đường Thử Nghiệm, Phường A, Thành phố B`, dùng với A0 khác A1. |
| P10 / P11 | `0912345678` / `09123456789`; lần lượt 10/11 chữ số, đều bắt đầu bằng `0`. Khi kiểm tra cập nhật điện thoại, P0 khác giá trị được thử. |
| P9 / P12 | `091234567` / `091234567890`; lần lượt 9/12 chữ số, giữ đúng kiểu ký tự và ký tự đầu. |
| P-PREFIX / P-TYPE | `1912345678` / `091234567A`; đều dài 10 ký tự, lần lượt chỉ sai ký tự đầu hoặc điều kiện toàn chữ số. |

Giữ phiên U hợp lệ và mục tiêu là U trong các đại diện điện thoại. Với dữ liệu invalid, giữ tên/địa chỉ ở N0/A0 đáp ứng các ràng buộc đã biết, chỉ thay số điện thoại mục tiêu. Mỗi biến thể valid bắt đầu từ dữ liệu nền được xác định riêng; không lấy thành công của lượt trước làm tiền điều kiện.

| REP-ID | Dữ liệu và thao tác đại diện | Lớp được phủ | Kỳ vọng theo yêu cầu | Lý do chọn / giới hạn |
|---|---|---|---|---|
| REP20-UPDATE | Mở Hồ sơ của U, đối chiếu dữ liệu nền; nhập N1, A1 và P10, đóng bàn phím rồi Cập nhật. Đăng xuất, đăng nhập lại U và xem Hồ sơ. Biến thể P11 chỉ đổi điện thoại, giữ N0/A0. | E20-SELF, E20-NAME, E20-ADDRESS, E20-P-VALID; E20-O-VIEW, E20-O-UPDATE | Truy cập được Hồ sơ của U. Với P10, lưu N1/A1/P10; với biến thể P11, giữ N0/A0 và lưu P11 (R20-01/02/03/05). | Gộp xem và cập nhật ba trường hợp lệ. P10/P11 dùng chung lớp EP và hai điểm biên hợp lệ, không thêm ca valid cùng mục tiêu. |
| REP20-P-SHORT | Hồ sơ U; chỉ thay điện thoại bằng P9 = `091234567`, yêu cầu lưu. | E20-P-SHORT, E20-O-PHONE-REJECT | Không lưu số điện thoại 9 chữ số này (R20-03). | Chỉ sai độ dài dưới khoảng; dùng chung điểm BVA dưới cận. |
| REP20-P-LONG | Hồ sơ U; chỉ thay điện thoại bằng P12 = `091234567890`, yêu cầu lưu. | E20-P-LONG, E20-O-PHONE-REJECT | Không lưu số điện thoại 12 chữ số này (R20-03). | Chỉ sai độ dài trên khoảng; dùng chung điểm BVA trên cận. |
| REP20-P-PREFIX | Hồ sơ U; chỉ thay điện thoại bằng `1912345678`, yêu cầu lưu. | E20-P-PREFIX, E20-O-PHONE-REJECT | Không lưu số điện thoại không bắt đầu bằng `0` (R20-03). | Giữ toàn chữ số và dài 10, chỉ sai ký tự đầu; không coi giá trị ký tự đầu là biên số. |
| REP20-P-TYPE | Hồ sơ U; chỉ thay điện thoại bằng `091234567A`, yêu cầu lưu nếu kênh nhập cho phép. | E20-P-TYPE, E20-O-PHONE-REJECT | Không lưu chuỗi chứa ký tự không phải chữ số (R20-03). | Giữ ký tự đầu `0` và độ dài chuỗi 10. Nếu bàn phím/ô nhập chặn ký tự, đối chiếu tại điểm chặn; không ép nhập bằng API hoặc tính là đã gửi chuỗi đó. |
| REP20-EMAIL | U; chạm ô Email và thử nhập `fr20.change@example.test` khác Email0 để kiểm tra cơ chế khóa chỉnh sửa. | E20-EMAIL, E20-O-EMAIL-KEEP | Email không bị thay đổi qua giao diện (R20-04). | Kiểm tra tại UI việc Email không cho sửa. Không nhận là đã gửi yêu cầu đổi Email hoặc đã kiểm tra bảo mật API. |
| REP20-ROLE | U có Role0 = `user`; yêu cầu tự đổi sang `admin`, giữ các dữ liệu khác hợp lệ. | E20-ROLE, E20-O-ROLE-KEEP | U không tự thay đổi được role (R20-06). | Giá trị role là dữ liệu tài khoản seed, không phải một miền được phép sửa. Chưa có đường tương tác hoặc cách đối chiếu role qua Mobile được xác nhận. |
| REP20-OTHER | Phiên U nhưng yêu cầu cập nhật hồ sơ V, dùng tên đề xuất N1; giữ dữ liệu khác đáp ứng ràng buộc đã biết. | E20-OTHER, E20-O-OTHER-PROTECTED | Hồ sơ V không bị yêu cầu của U cập nhật (R20-05). | U/V khác nhau theo tài khoản được kiểm soát. Định danh thực tế và đường tương tác phải được xác định; không tự tạo ô ID hoặc chuyển sang API. |

Các lớp đầu ra được đối chiếu cùng thao tác tạo ra chúng. REP20-EMAIL kiểm tra cơ chế khóa chỉnh sửa tại UI. REP20-ROLE/OTHER vẫn là đại diện thiết kế nhưng chưa có đường thực thi qua Hồ sơ Mobile; không nhận việc role/hồ sơ V giữ nguyên là bằng chứng đã thử yêu cầu bị cấm. Các tình huống thiếu quy định ở mục 4.2 không tự được gán valid/invalid.

### 4.4. Giá trị biên (BVA)

Nguồn: file `README.md` trong `eshop-sut`, FR-04/FR-20; slide “S04 — Domain Testing”.

**Biến: độ dài số điện thoại I20-PHONE; LB = 10, UB = 11, hai biên đóng; Δ = 1 chữ số** (R20-03). Giữ toàn chữ số và ký tự đầu `0` để chỉ xét độ dài. Quanh LB có 9–10–11; quanh UB có 10–11–12; gộp hai điểm trùng 10 và 11 thành bốn dữ liệu dưới đây.

| BVA-ID | Dữ liệu / độ dài | Lớp | Ca EP dùng chung | Kỳ vọng |
|---|---|---|---|---|
| B20-LOW | `091234567`; 9 = LB − Δ | E20-P-SHORT, E20-O-PHONE-REJECT | REP20-P-SHORT | Không lưu số điện thoại 9 chữ số (R20-03). |
| B20-AT-LB | `0912345678`; 10 = LB = UB − Δ | E20-P-VALID, E20-O-UPDATE | REP20-UPDATE với P10 | Được cập nhật số điện thoại này cho U khi các dữ liệu đi kèm hợp lệ (R20-02/03/05). |
| B20-AT-UB | `09123456789`; 11 = UB = LB + Δ | E20-P-VALID, E20-O-UPDATE | REP20-UPDATE với P11 | Được cập nhật số điện thoại 11 chữ số này cho U khi các dữ liệu đi kèm hợp lệ (R20-02/03/05); không giới hạn chỉ 10 chữ số. |
| B20-HIGH | `091234567890`; 12 = UB + Δ | E20-P-LONG, E20-O-PHONE-REJECT | REP20-P-LONG | Không lưu số điện thoại 12 chữ số (R20-03). |

Bốn điểm độ dài dùng chung dữ liệu và kỳ vọng EP, không tạo bộ biên trùng. Sai ký tự đầu và sai kiểu ký tự giữ các đại diện EP riêng; không áp ±1 lên giá trị số điện thoại, Email, role hoặc ID. SRS chưa quy định độ dài tên/địa chỉ nên không tạo biên cho các trường này. Điện thoại rỗng vẫn thuộc phần chưa đủ căn cứ ở mục 4.2, không tự coi là ca invalid bổ sung. Chuỗi thử nhập, giá trị còn trong ô và kết quả lưu được ghi theo quan sát ở mục 4.5; không suy ra giới hạn kênh nhập từ SRS.

### 4.5. Testcase và kết quả

**Tiền điều kiện và tiêu chí đánh giá:**

**PC chung:** Mobile đúng baseline; U = `test@eshop.com` đã đăng nhập, mở Hồ sơ của mình. Trước mỗi ca, lấy lại và ghi nhận N0/P0/A0/Email0/Role0 đã lưu; giữ N0/A0 đáp ứng ràng buộc đã biết. P0 là giá trị thực tế, có thể rỗng/null, không bắt buộc đang hợp lệ; mỗi ca thay toàn bộ số điện thoại bằng dữ liệu mục tiêu trước khi lưu. Dữ liệu mới N1/A1 và các số điện thoại theo mục 4.3. Mỗi ca xác định lại dữ liệu đã lưu bằng phiên đăng nhập mới; không mặc định thao tác của ca trước đã thành công hoặc dữ liệu nền luôn giống seed.

| Mã PC | Điều kiện riêng |
|---|---|
| PC20-UPDATE | P0 khác số điện thoại được thử. Với D01, N0 khác N1 và A0 khác A1 để nhận diện cả ba thay đổi; D02 giữ tên/địa chỉ ở N0/A0 và chỉ đổi điện thoại. |
| PC20-PHONE | Giữ N0/A0 và các thuộc tính được bảo vệ; chỉ thay điện thoại mục tiêu. Ghi chuỗi thử nhập, giá trị còn lại trong ô và giá trị đã lưu sau đăng xuất/đăng nhập lại. P0 ghi theo thực tế để đối chiếu trước/sau. |
| PC20-EMAIL | Mở Hồ sơ của U, xác định Email0. Ô Email có nhãn Email (Không đổi); thử chạm/nhập để đánh giá cơ chế khóa tại UI, không tạo yêu cầu sửa bằng API. |
| PC20-ROLE | U có Role0 = `user`; phải xác định được đường tương tác và nguồn quan sát role trên Mobile trước khi đánh giá yêu cầu đổi sang `admin`. |
| PC20-OTHER | V = `admin@eshop.com`, khác U, có hồ sơ nền được xác định và tên ban đầu khác N1. Cần luồng Mobile thực tế tạo yêu cầu tác động đến V từ phiên U và cách đối chiếu hồ sơ V bằng phiên hợp lệ riêng. |

Đóng bàn phím trước khi bấm Cập nhật; thao tác chỉ đóng bàn phím không được tính là đã gửi yêu cầu lưu. Với D01–D06, đăng xuất rồi đăng nhập lại U để đối chiếu dữ liệu từ server; chỉ rời rồi mở lại Hồ sơ có thể vẫn hiển thị dữ liệu chưa lưu trong bộ nhớ.

D07 đánh giá khóa chỉnh sửa Email tại UI. D08–D09 ghi **NOT RUN (chưa chạy)** vì chưa đạt tiền điều kiện về đường tương tác/nguồn quan sát; không suy ra bảo vệ quyền đã PASS. Nếu UI chặn hoặc chuyển đổi dữ liệu điện thoại, ghi đúng chuỗi hiệu lực và điểm chặn. Không đặt thông báo cụ thể hoặc yêu cầu hoàn tác các trường khác khi điện thoại sai.

**Bảng testcase:**

| TC-ID | Mục tiêu / dữ liệu / truy vết | Bước chạy cụ thể | Expected Result | Actual Result | Verdict | Bằng chứng |
|---|---|---|---|---|---|---|
| F20-D01 | Xem và cập nhật hồ sơ U; PC20-UPDATE; N1 = `Nguyễn Văn Kiểm Thử`, A1 = `20 Đường Thử Nghiệm, Phường A, Thành phố B`, P10 = `0912345678`; R20-01/02/03/05; REP20-UPDATE; E20-SELF, E20-NAME, E20-ADDRESS, E20-P-VALID, E20-O-VIEW, E20-O-UPDATE; B20-AT-LB | 1. Mở Hồ sơ U, ghi nhận dữ liệu đã lưu. 2. Thay tên bằng N1, địa chỉ bằng A1, điện thoại bằng P10; đóng bàn phím, bấm Cập nhật một lần và ghi phản hồi. 3. Đăng xuất, đăng nhập lại U, mở Hồ sơ và đối chiếu tên/điện thoại/địa chỉ với dữ liệu đã nhập. | Truy cập được Hồ sơ của U; tên, địa chỉ và điện thoại phản ánh N1/A1/P10 đã lưu cho U. Số điện thoại 10 chữ số bắt đầu bằng `0` được chấp nhận. | Nhập N1/A1/P10. Bấm Cập nhật hiện lỗi: “Số điện thoại không hợp lệ. Vui lòng nhập đúng 9-10 chữ số.”, không chấp nhận 0912345678. Rời rồi mở lại Hồ sơ trong cùng phiên vẫn hiện dữ liệu vừa nhập. Sau đăng xuất/đăng nhập lại, tên trở về Test User, điện thoại và địa chỉ rỗng; các thay đổi chưa được lưu. | FAIL | [Số hợp lệ 10 chữ số bị từ chối](screenshots/FR20/D01.jpg) |
| F20-D02 | Cập nhật điện thoại 11 chữ số; PC20-UPDATE; `09123456789`, giữ N0/A0; R20-02/03/05; REP20-UPDATE với P11; E20-SELF, E20-P-VALID, E20-O-VIEW, E20-O-UPDATE; B20-AT-UB | 1. Mở Hồ sơ U với dữ liệu đã xác định lại. 2. Chỉ thay điện thoại bằng `09123456789`, giữ N0/A0; đóng bàn phím rồi bấm Cập nhật một lần. 3. Đăng xuất, đăng nhập lại U và đối chiếu điện thoại đã lưu. | Điện thoại của U được cập nhật thành `09123456789`; không giới hạn hợp lệ chỉ ở 10 chữ số. | Ô giữ đủ 09123456789, gồm 11 chữ số. Bấm Cập nhật khi bàn phím đã đóng hiện lỗi yêu cầu 9-10 chữ số. Sau đăng xuất/đăng nhập lại, số điện thoại vẫn rỗng; số mới không được lưu. | FAIL | [Số hợp lệ 11 chữ số bị từ chối](screenshots/FR20/D02.jpg) |
| F20-D03 | Điện thoại thiếu độ dài; PC20-PHONE; `091234567`, 9 chữ số; R20-03; REP20-P-SHORT; E20-P-SHORT, E20-O-PHONE-REJECT; B20-LOW | 1. Mở Hồ sơ U và ghi P0 đã lưu. 2. Chỉ thay điện thoại bằng `091234567`, ghi chuỗi hiệu lực; đóng bàn phím rồi bấm Cập nhật một lần nếu UI cho phép. 3. Đăng xuất, đăng nhập lại U và đối chiếu số thực sự được lưu. | Không lưu số điện thoại 9 chữ số `091234567`; không đặt cơ chế chặn hoặc thông báo cụ thể. | Nhập đủ 091234567 (9 chữ số), đóng bàn phím rồi Cập nhật một lần. Hiện thông báo lỗi. Sau đăng xuất/đăng nhập lại, số điện thoại vẫn rỗng; không lưu chuỗi đã thử. | PASS | |
| F20-D04 | Điện thoại thừa độ dài; PC20-PHONE; `091234567890`, 12 chữ số; R20-03; REP20-P-LONG; E20-P-LONG, E20-O-PHONE-REJECT; B20-HIGH | 1. Mở Hồ sơ U và ghi P0 đã lưu. 2. Chỉ thay điện thoại bằng `091234567890`, ghi chuỗi hiệu lực; đóng bàn phím rồi bấm Cập nhật một lần nếu UI cho phép. 3. Đăng xuất, đăng nhập lại U và đối chiếu số thực sự được lưu. | Không lưu số điện thoại 12 chữ số `091234567890`; không tự quy định UI phải chặn bằng cách nào. | Nhập đủ 091234567890 (12 chữ số), đóng bàn phím rồi Cập nhật một lần. Hiện thông báo lỗi. Sau đăng xuất/đăng nhập lại, số điện thoại vẫn rỗng; không lưu chuỗi đã thử. | PASS | |
| F20-D05 | Điện thoại sai ký tự đầu; PC20-PHONE; `1912345678`, toàn chữ số, dài 10; R20-03; REP20-P-PREFIX; E20-P-PREFIX, E20-O-PHONE-REJECT | 1. Mở Hồ sơ U và ghi P0 đã lưu. 2. Chỉ thay điện thoại bằng `1912345678`, ghi chuỗi hiệu lực; đóng bàn phím rồi bấm Cập nhật một lần nếu UI cho phép. 3. Đăng xuất, đăng nhập lại U và đối chiếu số thực sự được lưu. | Không lưu số `1912345678` vì không bắt đầu bằng `0`. | Nhập 1912345678 dài 10 chữ số, đóng bàn phím rồi bấm Cập nhật. Ứng dụng báo “Cập nhật thành công!”. Sau đăng xuất/đăng nhập lại, điện thoại vẫn là 1912345678; số không bắt đầu bằng 0 đã được lưu. | FAIL | [Số sai ký tự đầu được báo cập nhật thành công](screenshots/FR20/D05.jpg) |
| F20-D06 | Điện thoại có ký tự không phải chữ số; PC20-PHONE; `091234567A`, dài 10 ký tự, bắt đầu bằng `0`; R20-03; REP20-P-TYPE; E20-P-TYPE, E20-O-PHONE-REJECT | 1. Mở Hồ sơ U và ghi P0 đã lưu. 2. Thử nhập/dán `091234567A` qua UI, ghi chuỗi hiệu lực. 3. Nếu giữ được chuỗi, đóng bàn phím, bấm Cập nhật một lần, sau đó đăng xuất/đăng nhập lại U để đối chiếu dữ liệu đã lưu; nếu UI chặn/chuyển đổi, ghi điểm chặn và giá trị hiệu lực. | Không lưu `091234567A`. Chặn ký tự tại UI chỉ chứng minh kết quả tại kênh nhập, không chứng minh đã kiểm tra lưu chính chuỗi invalid này. | Ô giữ nguyên 091234567A dù dùng bàn phím phone-pad. Đóng bàn phím, bấm Cập nhật hiện lỗi. Sau đăng xuất/đăng nhập lại, điện thoại trở về giá trị đã lưu trước ca là 1912345678; không lưu chuỗi có chữ A. | PASS | |
| F20-D07 | Bảo vệ Email qua cơ chế khóa UI; PC20-EMAIL; thử đổi Email0 sang `fr20.change@example.test`; R20-04; REP20-EMAIL; E20-EMAIL, E20-O-EMAIL-KEEP | 1. Mở Hồ sơ U, ghi Email0. 2. Chạm ô Email và thử nhập `fr20.change@example.test`, quan sát khả năng nhận focus/chỉnh sửa. 3. Đối chiếu Email vẫn là Email0. Đánh giá cơ chế khóa tại UI; không tự nhận đã gửi yêu cầu đổi Email. | Email không bị thay đổi qua giao diện; giữ Email0. Không yêu cầu bắt buộc phải có ô Email để thử nhập. | Ô Email hiển thị test@eshop.com kèm nhãn Email (Không đổi), bị khóa. Chạm ô và thử nhập fr20.change@example.test không thay đổi được Email. Không tạo/gửi được yêu cầu đổi Email qua giao diện. | PASS | |
| F20-D08 | Bảo vệ role — có điều kiện PC20-ROLE; `user` → `admin`, giữ dữ liệu khác hợp lệ; R20-06; REP20-ROLE; E20-ROLE, E20-O-ROLE-KEEP | 1. Xác định Role0 của U và đường tương tác Mobile thực tế theo PC. 2. Qua đường đó, thử yêu cầu tự đổi sang `admin`. 3. Đối chiếu role bằng nguồn quan sát Mobile đã xác định. Không thực hiện ca khi còn thiếu PC. | U không tự thay đổi được role; Role0 không bị yêu cầu này đổi thành `admin`. | Trong Hồ sơ chỉ có Email bị khóa, Họ Tên, Số điện thoại, Địa chỉ, Cập nhật và Thoát; không thấy trường/luồng tự đổi role hoặc nguồn hiển thị role trong Mobile. Chưa tạo được yêu cầu user → admin theo PC20-ROLE; chưa đánh giá khả năng chống tự đổi role. | NOT RUN | |
| F20-D09 | Bảo vệ hồ sơ người khác — có điều kiện PC20-OTHER; phiên U, mục tiêu V, tên đề xuất N1; R20-05; REP20-OTHER; E20-OTHER, E20-O-OTHER-PROTECTED | 1. Xác định hồ sơ nền V và luồng Mobile thực tế theo PC. 2. Giữ phiên U, dùng luồng đó yêu cầu đổi tên hồ sơ V thành N1; giữ dữ liệu khác hợp lệ. 3. Đối chiếu hồ sơ V bằng phiên Mobile hợp lệ riêng. Không thay ID bằng API hoặc tự tạo đường nhập. | Hồ sơ V không bị yêu cầu từ U cập nhật; tên V không bị đổi thành N1 bởi thao tác này. | Hồ sơ gắn với tài khoản đăng nhập; giao diện không có ô ID, danh sách người dùng hoặc lựa chọn hồ sơ mục tiêu khác U. Chưa tạo được yêu cầu sửa hồ sơ admin@eshop.com từ phiên U theo PC20-OTHER. Việc hồ sơ V không đổi không thay thế một lượt thử truy cập trái quyền. | NOT RUN | |

### 4.6. Truy vết và kết luận

**Truy vết thiết kế** — quy tắc hồ sơ lấy từ FR-04, chức năng Mobile lấy từ FR-20:

| Yêu cầu / lớp / biên | Ca hiện hành / giới hạn |
|---|---|
| R20-01: E20-O-VIEW; mở Hồ sơ Mobile | F20-D01, dùng chung luồng xem với các ca cập nhật |
| R20-02/05: E20-SELF, E20-NAME, E20-ADDRESS, E20-O-UPDATE; REP20-UPDATE | F20-D01; F20-D02 bổ sung điện thoại tại cận trên |
| R20-03: E20-P-VALID; B20-AT-LB, B20-AT-UB | F20-D01, F20-D02 |
| R20-03: E20-P-SHORT/P-LONG, E20-O-PHONE-REJECT; B20-LOW/B20-HIGH | F20-D03 / F20-D04 |
| R20-03: E20-P-PREFIX/P-TYPE, E20-O-PHONE-REJECT | F20-D05 / F20-D06; D06 cần ghi đúng chuỗi hiệu lực hoặc điểm chặn của UI |
| R20-04: E20-EMAIL, E20-O-EMAIL-KEEP; REP20-EMAIL | F20-D07 — kiểm tra khóa chỉnh sửa Email tại UI; không suy ra đã kiểm thử API. |
| R20-06: E20-ROLE, E20-O-ROLE-KEEP; REP20-ROLE | F20-D08 — NOT RUN; chưa có đường tự đổi role và nguồn quan sát role qua Mobile. |
| R20-05: E20-OTHER, E20-O-OTHER-PROTECTED; REP20-OTHER | F20-D09 — NOT RUN; chưa có đường yêu cầu sửa hồ sơ V từ phiên U qua Mobile. |
| B20-LOW / B20-AT-LB / B20-AT-UB / B20-HIGH: 9–10–11–12 chữ số | F20-D03 / F20-D01 / F20-D02 / F20-D04 |
| Điện thoại rỗng; tên/địa chỉ rỗng, chuẩn hóa hoặc giới hạn độ dài chưa có quy định | Giữ giới hạn ở mục 4.1–4.2; không thêm ca với kỳ vọng tự đặt. |

Bộ thiết kế gồm **7 ca F20-D01–D07 có thể thực hiện qua UI và 2 ca F20-D08–D09 chưa đủ tiền điều kiện**. Bốn điểm BVA dùng chung ca EP; sai ký tự đầu và sai kiểu ký tự mỗi điều kiện có một ca riêng. D01 gộp xem và cập nhật ba trường hợp lệ; D02 chỉ đổi điện thoại ở cận 11 chữ số. D07 kiểm tra cơ chế khóa Email tại UI.

D01–D07 ánh xạ **13/17 lớp trong thiết kế** ở mục 4.2; không đồng nghĩa mọi đầu ra kỳ vọng đều đã đạt. D08–D09 ánh xạ **4 lớp còn lại**, chưa được thực thi trong phạm vi Mobile.

**Kết quả thực thi:** 9 ca thiết kế, 7 ca đã thực thi, **4 PASS, 3 FAIL và 2 NOT RUN**. D01/D02 từ chối số điện thoại hợp lệ 10/11 chữ số bắt đầu bằng `0`; D05 lưu số không bắt đầu bằng `0`. Ba ca FAIL có ảnh minh chứng trong bảng testcase. D03/D04/D06 không lưu các chuỗi invalid tương ứng; D07 PASS cho khóa Email tại UI. D01 bị chặn ở điện thoại nên chưa kết luận riêng việc lưu tên/địa chỉ thành công. D08/D09 chưa thực thi vì thiếu đường tương tác qua Mobile; không kết luận hai yêu cầu bảo vệ quyền đã PASS.

## 5. Tổng kết

Báo cáo bao gồm **4 tính năng**, tương ứng bốn pool: FR-06 — Chi tiết sản phẩm (Web), FR-07 — Giỏ hàng (Web), FR-14 — Danh mục Admin và FR-20 — Hồ sơ Mobile, dùng quy tắc hồ sơ FR-04. Mỗi tính năng trình bày yêu cầu/input-output, EP, đại diện, BVA, testcase và truy vết. EP và BVA dùng chung các ca trùng dữ liệu/mục tiêu.

**Tổng hợp kết quả đã chốt:**

| Tính năng | Ca thiết kế | Đã thực thi | PASS | FAIL | NOT RUN |
|---|---:|---:|---:|---:|---:|
| FR-06 — Chi tiết sản phẩm | 8 | 8 | 0 | 8 | 0 |
| FR-07 — Giỏ hàng | 9 | 9 | 1 | 8 | 0 |
| FR-14 — Danh mục Admin | 4 | 4 | 3 | 1 | 0 |
| FR-20 — Hồ sơ Mobile | 9 | 7 | 4 | 3 | 2 |
| **Tổng** | **30** | **28** | **8** | **20** | **2** |


**AI Critique — 200–300 từ:**

AI hỗ trợ chuyển SRS thành bảng input/output, phân lớp, đại diện và BVA, đồng thời giảm các ca trùng mục tiêu. Tuy nhiên, đầu ra có giới hạn khi chưa quan sát giao diện và cách ứng dụng lưu dữ liệu. Ví dụ, thiết kế Hồ sơ Mobile ban đầu dùng thao tác rời rồi mở lại màn hình để kiểm tra lưu. Kết quả F20-D01 cho thấy dữ liệu vừa nhập vẫn xuất hiện trong cùng phiên dù cập nhật bị từ chối. Người kiểm thử đã bổ sung đăng xuất và đăng nhập lại, nhờ đó phân biệt trạng thái giao diện với dữ liệu được lưu. Cách quan sát ban đầu chưa đủ mạnh vì AI dựa vào luồng chức năng tổng quát, chưa biết cơ chế giữ trạng thái của ứng dụng. Tương tự, các yêu cầu đổi role hoặc sửa hồ sơ người khác có oracle trong SRS nhưng không có đường thao tác qua Mobile đã xác định; vì vậy D08/D09 phải giữ NOT RUN, không kết luận PASS chỉ vì chưa thấy dữ liệu thay đổi. Quá trình review cũng cần gộp EP với BVA và giữ các giới hạn chưa được đặc tả. Bài học là dùng AI để tạo cấu trúc và truy vết, còn người kiểm thử phải kiểm tra tính khả thi, nguồn dữ liệu và độ tin cậy của phép quan sát. Kết quả FAIL cần được phân tích thành lỗi độc lập; không dùng số ca thất bại làm số bug hoặc dựng thiếu sót AI từ kết quả kiểm thử cũ.

## 6. Mandatory Disclosure

> Báo cáo, bộ testcase và skill này có phiên bản đầu được tạo với sự hỗ trợ của OpenAI Codex. Tôi đã rà soát và yêu cầu chỉnh sửa phạm vi bốn FR, phân lớp, dữ liệu đại diện, BVA và bộ ca theo SRS; yêu cầu bổ sung số lượng âm và ô rỗng cho FR-06, gộp các ca trùng mục tiêu và giữ rõ các trường hợp chưa đủ căn cứ. Tôi trực tiếp thực thi testcase, chụp ảnh và cung cấp các quan sát thực tế đã chốt trong báo cáo. Codex hỗ trợ hướng dẫn, diễn đạt, tổng hợp kết quả, kiểm tra số liệu và tham chiếu; không trực tiếp chạy các ca hoặc tạo ảnh minh chứng của bài làm hiện tại. Phần Tổng kết, AI Critique và khai báo này cũng có hỗ trợ soạn thảo của Codex. Tôi không dùng công cụ AI khác cho bài làm hiện tại.

| Phần có hỗ trợ AI | Đóng góp được khai báo |
|---|---|
| Khung tài liệu và skill | Chat hướng dẫn và chat làm bài bằng OpenAI Codex hỗ trợ tạo cấu trúc, đọc yêu cầu và xây dựng skill tái sử dụng. |
| Thiết kế ở mục 1–4 | Codex hỗ trợ input/output, EP, đại diện, BVA, testcase và truy vết; phạm vi và các lần sửa được lưu trong prompt log. |
| Kết quả và bằng chứng | Người làm bài trực tiếp thực thi, cung cấp ảnh/quan sát và chốt verdict; Codex hỗ trợ ghi nhận và trình bày, không sinh kết quả hay bằng chứng thay người kiểm thử. |
| Hoàn thiện báo cáo | Codex tổng hợp số liệu từ testcase, hỗ trợ AI Critique, Mandatory Disclosure và kiểm tra tham chiếu; không thay đổi kết quả đã chốt. |
