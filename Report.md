# Báo cáo HW02 — khung chưa điền

Đây là khung do AI hỗ trợ tạo, chưa phải báo cáo hoàn tất.

## 1. Phạm vi, yêu cầu nguồn và môi trường

Chưa điền. Phân biệt dữ liệu tham khảo trước đây với lần thực hiện mới.

## 2. FR-06 — Chi tiết sản phẩm

Phạm vi kiểm thử là trang chi tiết sản phẩm trên Web: thông tin sản phẩm, số lượng và thao tác thêm giỏ có phản hồi trực quan. Thiết kế được hỗ trợ bởi [skill domain-testing-bva](skills/domain-testing-bva/SKILL.md).

Bộ kiểm thử hiện hành gồm **8 ca**, bao phủ thông tin sản phẩm, số lượng hợp lệ, biên dưới, số âm, phần lẻ, chuỗi sai kiểu và ô rỗng. Các quan sát được giữ đúng theo lượt thao tác đã ghi; thao tác điều tra tiếp được trình bày riêng ở ghi chú thực thi.

### 2.1. Input/output và ràng buộc

Nguồn yêu cầu: file `README.md` trong `eshop-sut`, mục SRS, FR-06. Phương pháp: slide “S04 — Domain Testing”. Mã R06 là mã truy vết cục bộ của thiết kế này.

| Mã     | Yêu cầu FR-06                                                                                |
| ------ | -------------------------------------------------------------------------------------------- |
| R06-01 | Hiển thị đầy đủ ảnh lớn, tên, giá, mô tả và danh mục của sản phẩm.                           |
| R06-02 | Có ô Số lượng, chỉ nhận số nguyên dương, tối thiểu 1.                                        |
| R06-03 | Có thao tác Thêm vào giỏ hàng; sau khi bấm có phản hồi trực quan: toast hoặc badge cập nhật. |

Phạm vi kiểm thử gồm ba yêu cầu FR-06 nêu trên. Sau thao tác thêm, đối chiếu sản phẩm và số lượng trong giỏ, đồng thời kiểm tra phản hồi trực quan bằng toast notification hoặc badge cập nhật.

| ID    | Loại / biến                          | Ràng buộc hoặc kỳ vọng có căn cứ                                                                      | Phần chưa được quy định                                         |
| ----- | ------------------------------------ | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| I06-P | Input: sản phẩm P được chọn trên Web | P = iPhone 15 Pro Max, ID 1 trên cơ sở dữ liệu seed sạch.                                             | Hành vi với sản phẩm không tồn tại nằm ngoài phạm vi bộ ca này. |
| I06-S | Input: chuỗi s nhập vào ô Số lượng   | Phân biệt chuỗi được gõ/dán với số lượng hiệu lực q sau khi rời ô/submit.                             | Mặc định, rỗng, khoảng trắng, cú pháp số và cách chuẩn hóa.     |
| I06-Q | Input: số lượng hiệu lực q           | Phần miền đã biết: q là số nguyên và q ≥ 1; cận dưới đóng tại 1.                                      | Cận trên, giới hạn kỹ thuật và tồn kho.                         |
| I06-A | Input: bấm Thêm vào giỏ một lần      | Sau khi bấm có toast notification hoặc badge cập nhật theo R06-03; với q hợp lệ, thêm đúng P/q.       | Không tự đặt nội dung/thời lượng thông báo hoặc HTTP status.    |
| S06   | Trạng thái / tiền điều kiện          | Phiên đã đăng nhập theo thống nhất; giỏ trống trước mỗi lần thêm để cô lập ca.                        | Không đánh giá chính sách giỏ khách trong bộ ca này.            |
| O06-D | Output: chi tiết sản phẩm            | Đủ năm thành phần, nội dung tương ứng dữ liệu P; ảnh lớn của trang chi tiết hiển thị được.            | Chưa có ngưỡng pixel hoặc quy tắc fallback ảnh thiếu.           |
| O06-A | Output: kết quả thêm và phản hồi     | Sau khi bấm có toast notification hoặc badge cập nhật; việc thêm P/q chỉ hợp lệ khi q đáp ứng R06-02. | Câu chữ, thời gian và kiểu đếm badge không được quy định.       |
| O06-V | Output: kiểm soát q sai              | Không dùng chính q không nguyên dương để thêm giỏ, suy từ R06-02.                                     | Chặn nhập, chặn submit, tự sửa hay báo lỗi chưa được quy định.  |

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
| P.price                  | `30000000` ₫; FR-06 không chỉ định kiểu dấu phân cách tiền.                                 |
| P.description            | `Điện thoại cao cấp của Apple`                                                              |
| P.category               | `Điện thoại`, `category_id = 1`                                                             |
| P.image                  | `https://placehold.co/300x300/png?text=iPhone+15`; ảnh placeholder có nội dung “iPhone 15”. |
| Đường dẫn sản phẩm       | `/product/1`, theo route `/product/:id` trong `frontend-web/src/App.jsx`.                   |
| Tài khoản thử            | `test@eshop.com`, vai trò người dùng theo seed.                                             |
| Trạng thái trước ca thêm | Đăng nhập hợp lệ; trang P tải xong; giỏ trống trước mỗi ca.                                 |

Seed tạo lại bảng và dữ liệu khi backend khởi động. Fixture trên mô tả baseline chuẩn, không xác nhận server đã được khởi chạy hoặc ca nào đã thực thi.

### 2.2. Phân lớp tương đương

Hai điều kiện của số lượng: **C1** là số nguyên; **C2** là ≥ 1 khi đã là số nguyên. Với ca sai kiểu, không so sánh chuỗi với cận số. Các ca chỉ thay đổi số lượng; giữ nguyên P, phiên đăng nhập và điều kiện giỏ để cô lập mục tiêu.

| EC-ID            | Miền/lớp                                                         | Kỳ vọng theo ba yêu cầu FR-06                                                                                                                                                  |
| ---------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| E06-P            | P tồn tại, có dữ liệu seed chuẩn                                 | Đối chiếu đủ ảnh lớn, tên, giá, mô tả, danh mục; R06-01.                                                                                                                       |
| E06-Q1           | q nguyên, q ≥ 1 trong phần miền SRS đã quy định                  | Nhận q hợp lệ; thêm đúng P/q và có toast hoặc badge cập nhật; R06-02/03.                                                                                                       |
| E06-Q2           | q nguyên, q ≤ 0                                                  | Không thêm chính q không dương; R06-02. Đại diện 0 đồng thời là điểm BVA sát biên; -1 bổ sung tình huống số âm trong cùng lớp.                                                 |
| E06-Q3           | Số có phần lẻ, đại diện 1.5                                      | Không thêm q có phần lẻ; R06-02. 1.5 chỉ sai C1, không chọn thêm giá trị dưới 1.                                                                                               |
| E06-Q4           | Chuỗi không phải số nguyên, đại diện abc                         | Không dùng chuỗi sai kiểu làm số lượng; R06-02. Tách khỏi số lẻ vì hai đường nhập có thể xử lý khác nhau; bản ghi đã có cũng phân biệt ô giữ 1.5 với abc bị chuyển thành rỗng. |
| X06-EMPTY        | Chuỗi rỗng, độ dài 0                                             | Chính sách required/default/chuẩn hóa chưa được quy định; phản hồi sau khi bấm vẫn được đối chiếu R06-03.                                                                      |
| E06-O1 / O2 / O3 | Output hiển thị P / phản hồi sau bấm và thêm hợp lệ / ngăn q sai | Truy vết tương ứng R06-01 / R06-03 / R06-02; lỗi/thông báo đúng kỳ vọng không phải output sai.                                                                                 |

Q1 và Q2 rời nhau, phủ miền số nguyên; Q3 xét số có phần lẻ, Q4 xét chuỗi sai kiểu. Ô rỗng được giữ như nhóm khám phá X06-EMPTY, chưa phải lớp valid/invalid đã xác nhận. Khoảng trắng, các cách viết số và ngưỡng số lượng lớn vẫn là khoảng trống đặc tả, không có ca riêng trong bộ hiện hành. Không tự đặt max, tồn kho hoặc thông báo lỗi.

### 2.3. Chọn đại diện và gộp mục tiêu

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

Không có UB được SRS quy định nên không đặt biên 99/100 hay giới hạn tồn kho. -1 được giữ để thử số âm trong cùng lớp không dương, không phải điểm BVA mới ngoài 0–1–2. Rỗng là ca khám phá, không phải một giá trị số dùng cho BVA; 1.5 và abc là ca EP sai kiểu. Các trường sản phẩm chỉ được xem ở FR-06, không có đầu vào chỉnh giá/tên hay biên định lượng ảnh để thiết kế thêm BVA.

### 2.5. Bảng testcase rút gọn

#### 2.5.1. Tiền điều kiện và cách đọc kết quả

**PC:** P theo fixture trên; phiên đăng nhập hợp lệ; trang P tải xong; giỏ trống trước mỗi ca thêm. Mỗi ca thêm độc lập, không bấm lặp hoặc dùng giỏ còn dữ liệu từ ca trước.

**Mục tiêu phản hồi R06-03:** SRS yêu cầu sau khi bấm Thêm có phản hồi trực quan bằng toast notification hoặc badge cập nhật, không nêu điều kiện chỉ áp dụng cho số lượng hợp lệ. Các ca D02–D08 đều kiểm tra mục tiêu này sau một lần bấm. Nếu không có cả hai hình thức phản hồi thì mục tiêu R06-03 không đạt; không bắt buộc cả hai cùng xuất hiện và không tự đặt nội dung thông báo cụ thể.

**Mục tiêu số lượng R06-02:** không ghi số lượng ngoài miền nguyên dương vào giỏ. Việc không có phản hồi đã đủ để đánh giá R06-03, nhưng không tự chứng minh validation số lượng đúng hoặc sai. Với dữ liệu sai/rỗng, giữ riêng nhận định về giá trị hiệu lực và kết quả giỏ; chính sách mặc định, chuẩn hóa hoặc nguyên văn thông báo lỗi chưa được đặc tả.

Actual Result trình bày các quan sát thực thi; kết quả bấm lần thứ hai được ghi riêng bên dưới, phân biệt với kết quả của một lần bấm. Cột Bằng chứng / Issue dành cho ảnh/video và liên kết tương ứng.

| TC-ID   | Mục tiêu / dữ liệu / truy vết                                                                                           | Bước chạy cụ thể                                                                                                                                                        | Expected Result / điều cần quan sát                                                                                                                                                                                                                                                                          | Actual Result                                                                                                                                                                                                       | Verdict | Bằng chứng / Issue                                                                            |
| ------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------- | --------------------------------------------------------------------------------------------- |
| F06-D01 | Kiểm tra đủ thông tin sản phẩm và điều khiển; P; R06-01/02/03; REP06-P; E06-P/O1                                        | 1. Chọn P từ danh sách, mở trang chi tiết. 2. Đối chiếu ảnh, tên, giá, mô tả, danh mục với fixture P. 3. Kiểm tra có ô Số lượng và nút Thêm.                            | Đủ năm thành phần của đúng P; ảnh lớn của trang chi tiết hiển thị được; tên/giá/mô tả/danh mục đúng nguồn chuẩn; có ô Số lượng và thao tác Thêm. Không đặt ngưỡng pixel hoặc quy tắc định dạng giá ngoài FR-06.                                                                                              | Ảnh, tên, giá và mô tả đúng fixture; có ô Số lượng và nút Thêm; không thấy danh mục Điện thoại.                                                                                                                     | FAIL    | [Trang chi tiết sản phẩm](screenshots/FR06/D01.png)                                           |
| F06-D02 | Kiểm tra thêm q = 1 và phản hồi toast notification hoặc badge cập nhật; q = 1; R06-02/03; E06-Q1/O2; REP06-V; B06-AT    | Thay toàn bộ nội dung ô bằng 1, rời ô, bấm Thêm một lần, quan sát toast notification hoặc badge cập nhật và P/q trong giỏ.                                              | Nhận 1; thêm đúng P với q = 1; có toast notification hoặc badge cập nhật. Không yêu cầu cả hai hay một giá trị badge cố định.                                                                                                                                                                                | Ô giữ 1; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống.                                                                                                  | FAIL    | [Ô số lượng 1](screenshots/FR06/D02-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D02-2.png)    |
| F06-D03 | Kiểm tra thêm q = 2 và phản hồi toast notification hoặc badge cập nhật; q = 2; R06-02/03; E06-Q1/O2; REP06-V; B06-ABOVE | Thay toàn bộ nội dung ô bằng 2, rời ô, bấm Thêm một lần, quan sát toast notification hoặc badge cập nhật và P/q trong giỏ.                                              | Nhận 2; thêm đúng P với q = 2; có toast notification hoặc badge cập nhật.                                                                                                                                                                                                                                    | Ô giữ 2; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống.                                                                                                  | FAIL    | [Ô số lượng 2](screenshots/FR06/D03-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D03-2.png)    |
| F06-D04 | Kiểm tra số lượng 0 và kiểm tra phản hồi sau bấm Thêm; q = 0; R06-02/03; E06-Q2/O3; REP06-LB; B06-LOW                   | Thay nội dung ô bằng 0, rời ô; thử Thêm một lần; quan sát giá trị hiệu lực, toast notification/badge và giỏ.                                                            | Sau một lần bấm phải có toast notification hoặc badge cập nhật (R06-03). Không thêm P với q = 0. Chặn hoặc tự sửa được đối chiếu với oracle ở 2.5.1; cơ chế cụ thể chưa được quy định.                                                                                                                       | Ô giữ 0; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. Chưa tách được validation do ca số lượng hợp lệ cũng không thêm được.                            | FAIL    | [Ô số lượng 0](screenshots/FR06/D04-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D04-2.png)    |
| F06-D05 | Kiểm tra số lượng âm và kiểm tra phản hồi sau bấm Thêm; q = -1; R06-02/03; E06-Q2/O3; REP06-N                           | Thay nội dung ô bằng -1, rời ô; bấm Thêm một lần; quan sát điểm chặn/giá trị giữ lại, toast notification/badge và giỏ.                                                  | Sau một lần bấm phải có toast notification hoặc badge cập nhật (R06-03). Không thêm P với q = -1; không tự yêu cầu một thông báo lỗi cụ thể.                                                                                                                                                                 | Ô giữ -1; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. Chưa tách được validation do ca số lượng hợp lệ cũng không thêm được.                           | FAIL    | [Ô số lượng -1](screenshots/FR06/D05-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D05-2.png)   |
| F06-D06 | Kiểm tra số lượng có phần lẻ và kiểm tra phản hồi sau bấm Thêm; q = 1.5; R06-02/03; E06-Q3/O3; REP06-F                  | Thay nội dung ô bằng 1.5, rời ô; quan sát phần lẻ; bấm Thêm một lần, đối chiếu q và toast notification/badge.                                                           | Sau một lần bấm phải có toast notification hoặc badge cập nhật (R06-03). Không thêm P với số lượng có phần lẻ 1.5. Nếu UI biến đổi dữ liệu, đối chiếu số lượng hiệu lực, không coi chuỗi ban đầu đã được chấp nhận nguyên trạng.                                                                             | Ô giữ 1.5; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. Chưa tách được validation do ca số lượng hợp lệ cũng không thêm được.                          | FAIL    | [Ô số lượng 1.5](screenshots/FR06/D06-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D06-2.png)  |
| F06-D07 | Kiểm tra chuỗi sai kiểu và kiểm tra phản hồi sau bấm Thêm; s = abc; R06-02/03; E06-Q4/O3; REP06-T                       | Thay nội dung ô bằng abc, rời ô; bấm Thêm một lần; quan sát dữ liệu giữ lại, toast notification/badge và giỏ.                                                           | Sau một lần bấm phải có toast notification hoặc badge cập nhật (R06-03). Không dùng abc làm số lượng thêm giỏ. Nếu ô thành rỗng, chính sách xử lý rỗng chưa được SRS quy định; không tự gán lỗi mới.                                                                                                         | UI không giữ chuỗi abc, ô thành rỗng; sau một lần bấm Thêm, không quan sát thấy toast notification và không thấy badge cập nhật; giỏ vẫn trống. Chưa kết luận được xử lý giá trị rỗng; chưa quan sát NaN trong giỏ. | FAIL    | [Ô số lượng rỗng](screenshots/FR06/D07-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D07-2.png) |
| F06-D08 | Kiểm tra ô rỗng và kiểm tra phản hồi sau bấm Thêm; Chuỗi rỗng, độ dài 0; X06-EMPTY; REP06-EMPTY; R06-02/03              | Chọn toàn bộ nội dung ô rồi Delete, không nhập thay thế; rời ô; bấm Thêm một lần; quan sát giá trị còn lại, toast notification/badge và số lượng trong giỏ nếu có thêm. | Sau một lần bấm phải có toast notification hoặc badge cập nhật (R06-03). Ô giữ rỗng, khôi phục giá trị hay đi mặc định? Nếu thêm, q hiệu lực là gì? Chưa có quy tắc xử lý rỗng nên không buộc mặc định về 1 hoặc hiện một lỗi cụ thể; vẫn đối chiếu R06-02 nếu quan sát được q không nguyên dương được thêm. | Sau khi xóa toàn bộ nội dung, ô Số lượng vẫn rỗng. Bấm Thêm một lần, không quan sát thấy toast notification và không thấy badge cập nhật. Giỏ hàng trống.                                                           | FAIL    | [Ô số lượng rỗng](screenshots/FR06/D08-1.png)<br>[Giỏ hàng trống](screenshots/FR06/D08-2.png) |

**Ghi chú thực thi — thử ấn tiếp lần thứ hai**

Thử ấn tiếp lần thứ hai trên cùng trang để kiểm tra thêm.

- **F06-D04 — số lượng `0`:** Nhập `0`, ấn hai lần thì vẫn thêm được sản phẩm vào giỏ với số lượng `0`. [Ảnh giỏ có số lượng 0](screenshots/FR06/D04-3.png).
- **F06-D05 — số lượng `-1`:** Nhập `-1`, ấn hai lần thì vẫn thêm được sản phẩm vào giỏ với số lượng `-1`. [Ảnh giỏ có số lượng -1](screenshots/FR06/D08-3.png).
- **F06-D08 — ô rỗng:** Xóa ô nhập cho rỗng, ấn hai lần thì số lượng, thành tiền và tổng tiền trong giỏ hiện `NaN`. [Ảnh giỏ hiển thị NaN](screenshots/FR06/D08-3.png).

#### 2.5.2. Truy vết và số ca

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
| Khám phá X06-EMPTY; REP06-EMPTY                                         | D08             |

Bộ hiện hành có **8 ca = 1 ca thông tin/điều khiển + 2 ca nguyên dương/BVA + 2 ca không dương (0 và -1) + 1 ca phần lẻ + 1 ca chuỗi sai kiểu + 1 ca khám phá rỗng**. Ba điểm BVA vẫn là 0–1–2; X02–X09 không thuộc bộ hiện hành. Bộ hiện hành dùng mã F06-D01–D08; log lịch sử giữ nguyên mã tại thời điểm ghi nhận. Đây là độ phủ thiết kế, không phải tuyên bố đã thực thi thành công.

### 2.6. Kết luận

Phần sử dụng AI và đánh giá artifact được trình bày tại [AI Critique và Audit](AI_Critique_and_Audit.md), các tương tác đã lưu ở [prompt log](Appendix_A_Prompt_Log.md).

Kết quả một lần bấm được trình bày trong bảng testcase. Các quan sát hai lần bấm của D04, D05 và D08 được ghi riêng phía trên; không suy diễn kết quả hai lần bấm cho D06 hoặc D07. Ảnh/video, thời điểm thực thi và liên kết issue được bổ sung theo bằng chứng thực tế; không đặt trước tên ảnh hoặc URL issue.

Bộ hiện hành có **8 ca với verdict FAIL**: D01 vi phạm R06-01 do thiếu danh mục; D02–D08 không đáp ứng phản hồi sau khi bấm theo R06-03. Nhận định về validation được giữ riêng trong Actual Result và các ghi chú D04, D05, D08. Nhiều ca cùng thất bại về một hành vi không đồng nghĩa có tám lỗi độc lập; số bug được tổng hợp từ báo cáo lỗi và GitHub Issues thực tế.

## 3. FR-07 — Giỏ hàng

### 3.1. Input/output và yêu cầu nguồn

Chưa thực hiện.

### 3.2. Equivalence partitions

Chưa thực hiện.

### 3.3. Đại diện và test cases

Chưa thực hiện. Không điền Actual/Verdict trước khi chạy.

### 3.4. Boundary Value Analysis

Chưa thực hiện.

### 3.5. Đánh giá thiết kế

Chưa thực hiện.

### 3.6. Thực thi và bằng chứng

Chưa thực hiện trong quy trình mới.

### 3.7. Liên kết bug report và GitHub Issues

Liên kết báo cáo lỗi và GitHub Issues được bổ sung sau khi hoàn thành báo cáo lỗi và tạo issue.

### 3.8. AI audit và gap quan sát được

Chưa đánh giá. Không dựng thiếu sót AI.

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
