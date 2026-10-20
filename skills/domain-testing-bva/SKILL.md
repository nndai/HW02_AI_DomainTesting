---
name: domain-testing-bva
description: >-
  Hướng dẫn thiết kế Domain Testing và Boundary Value Analysis từ yêu cầu được cung cấp: xác định input/output, phân lớp tương đương, chọn đại diện, phân tích biên và lập test case có truy vết. Dùng cho từng tính năng với sinh viên review sau mỗi bước; ghi rõ thông tin còn thiếu, không thực thi kiểm thử hoặc viết mô tả bug report.
---

# Domain Testing và BVA

## Phạm vi và nguồn

Hỗ trợ thiết kế cho một tính năng và nền tảng đã được người dùng chọn. Skill này chưa phải minh chứng đã áp dụng hay đã kiểm thử tính năng nào.

Trong bài HW02, đọc các nguồn sau từ thư mục chứa skill:

- [Slide S04](../../references/S04_Domain_Testing.txt): trang 3–6 về input/output và EP; trang 8–11 về cách chia lớp; trang 21–23 về đại diện/test cases; trang 24–27 về BVA; trang 28 về giới hạn kỹ thuật.
- [Assignment](../../references/Assignment.md): mục 2, 6–9 và 12 về quy trình từng bước, review, khả năng tái sử dụng, audit và commit do sinh viên thực hiện.
- [START_HERE](../../START_HERE.md) và phần liên quan trong [SRS](../../references/SRS.md). Dùng SRS làm oracle nghiệp vụ; đọc [API Specification](../../references/API_Specification.md) khi kênh API thuộc phạm vi.

Khi tái sử dụng ngoài bài này, nhận đặc tả, hướng dẫn kỹ thuật và quy ước log tương ứng từ người dùng. Nếu thiếu nguồn, báo rõ nguồn cần bổ sung. Không mang ràng buộc của tính năng hay ví dụ cũ sang tính năng mới.

Phân biệt yêu cầu được đặc tả, giả định chờ xác nhận và quan sát triển khai. Ghi tên tài liệu cùng mục/trang hoặc dòng cho từng quy tắc. Nêu mâu thuẫn giữa nguồn; không tự suy ra độ dài tối đa, tính duy nhất, trim khoảng trắng, tồn kho, mã lỗi hoặc thông báo lỗi chính xác khi chưa có căn cứ.

## Cách điều phối và điểm dừng

- Chỉ thực hiện bước được yêu cầu. Nếu người dùng yêu cầu áp dụng toàn bộ quy trình, bắt đầu bằng Bước 1 và dừng tại đó.
- Với mỗi bước: đọc phần đã được chấp nhận → làm đúng bước hiện tại → trình bày bảng và lý do → liệt kê thông tin còn thiếu → ghi log → dừng để sinh viên review.
- Kết thúc bằng trạng thái `Bước N — chờ review`, nêu các quyết định cần xác nhận và bước tiếp theo dự kiến. Chỉ chuyển bước sau khi người dùng xác nhận/yêu cầu tiếp tục; không coi im lặng hoặc câu hỏi góp ý là chấp thuận.
- Nếu có yêu cầu sửa, sửa chính bước đó rồi dừng lại. Khi một giả định thay đổi, chỉ rõ các EP/đại diện/biên/test case phụ thuộc cần review lại.
- Giữ bản AI ban đầu và phần sinh viên sửa có thể phân biệt. Không tự gắn nhãn đã được sinh viên review hoặc tự chấm audit thay sinh viên.

Quy trình dưới đây giữ bốn bước cốt lõi của S04; bước cuối tổng hợp các ca EP và BVA thành bộ test case chung. Không tự thực hiện các bước này chỉ vì đang đọc hoặc kiểm tra cấu trúc skill.

## Bước 1 — Xác định input/output và điều kiện

1. Ghi tính năng, nền tảng/kênh nhập, thao tác trong phạm vi, tiền điều kiện và yêu cầu nguồn. Nếu phạm vi chưa rõ, đánh dấu cần xác nhận, không mở rộng sang tính năng khác.
2. Xác định input người dùng, tham số hệ thống và trạng thái/dữ liệu có ảnh hưởng; tách input khỏi output quan sát được như dữ liệu hiển thị, thay đổi trạng thái, phản hồi thành công hoặc lỗi.
3. Với mỗi biến, ghi kiểu, đơn vị, miền giá trị, bắt buộc/tùy chọn, ràng buộc liên trường và căn cứ nếu đã được quy định. Đánh dấu `Chưa đủ thông tin` cho phần chưa có nguồn; chưa tạo EP hay dữ liệu test.

Đầu ra: bảng `ID | Input/Output/Trạng thái | Kiểu/đơn vị | Điều kiện/ràng buộc | Kết quả kỳ vọng theo nguồn | Nguồn | Điểm cần xác nhận`.

**Dừng để sinh viên review Bước 1.**

## Bước 2 — Chia lớp tương đương (EP)

1. Dựa trên các điều kiện đã được chấp nhận ở Bước 1, chia miền của từng input/output thành các lớp có cùng cách xử lý hoặc cùng loại kết quả mong đợi. Không yêu cầu mọi dữ liệu trong lớp phải cho cùng một giá trị số đầu ra.
2. Với điều kiện đầu vào, phân biệt lớp valid và invalid. Với khoảng giá trị, xét miền trong/ngoài khoảng theo biên đóng/mở; với tập giá trị được xử lý khác nhau, tách các nhóm tương ứng; với điều kiện “must be”, xét thỏa/không thỏa. Lớp đầu ra phân theo kết quả kỳ vọng thành công/lỗi theo đặc tả; thông báo lỗi đúng kỳ vọng không phải là đầu ra sai.
3. Kiểm tra chồng lấn và khoảng hở trong cùng một điều kiện. Nếu có lý do các phần tử được xử lý khác nhau, tách lớp và giải thích. Với ràng buộc liên trường, ghi rõ tổ hợp và điều kiện phụ thuộc.
4. Mỗi lớp có ID ổn định, điều kiện thành viên, loại lớp, kết quả kỳ vọng và nguồn. Trường hợp thiếu oracle ghi `Chờ xác nhận`, chưa kết luận valid/invalid và chưa tính là lớp đã được xác nhận.

Đầu ra: bảng `EC-ID | Biến/điều kiện | Miền/lớp | Loại lớp (input valid/invalid, output thành công/lỗi, hoặc chờ xác nhận) | Kết quả kỳ vọng | Nguồn/lý do`; kèm điểm chưa phủ hoặc chưa rõ. Chưa chọn đại diện.

**Dừng để sinh viên review Bước 2.**

## Bước 3 — Chọn đại diện và ca EP nháp

1. Chọn ít nhất một đại diện cho mỗi EC đã được xác nhận và giải thích vì sao đại diện thuộc lớp đó. Với lớp đầu ra, chọn bộ input tạo ra lớp đầu ra tương ứng theo đặc tả.
2. Ca valid có thể kết hợp nhiều lớp valid để giảm trùng lặp nhưng vẫn phủ đủ. Với mỗi ca invalid, chỉ vi phạm một điều kiện đầu vào/lớp invalid mục tiêu; giữ các điều kiện đầu vào còn lại hợp lệ để tránh lỗi này che lỗi khác. Truy vết lớp đầu ra như kết quả kỳ vọng của ca, không tính nó là vi phạm đầu vào thứ hai. Nếu không thể cô lập do phụ thuộc, nêu lý do.
3. Ghi tiền điều kiện và dữ liệu nền cần có; không khẳng định chúng đã tồn tại trong SUT. Chỉ ánh xạ đại diện/ca EP nháp tới EC, chưa tự sinh thêm bộ BVA.

Đầu ra: bảng `REP-ID/Ca EP nháp | Dữ liệu/tiền điều kiện | EC được phủ | Kết quả kỳ vọng và nguồn | Lý do chọn | Điểm chờ xác nhận`.

**Dừng để sinh viên review Bước 3.**

## Bước 4 — Phân tích giá trị biên (BVA)

1. Chỉ áp dụng BVA cho miền có thứ tự: số, độ dài, ngày/giờ hoặc đại lượng được đặc tả. Với miền danh nghĩa không có thứ tự, ghi `Không áp dụng` và lý do; không tạo biên số giả.
2. Xác định LB/UB, biên đóng/mở, đơn vị và bước lân cận phù hợp kiểu/độ chính xác. Xét ngay dưới, tại và ngay trên từng biên có căn cứ; không áp dụng ±1 máy móc cho số thực hoặc thời gian.
3. Nếu chỉ biết một biên, phân tích biên đã biết và ghi biên kia chưa được quy định. Không tự đặt UB; không dùng vô cực làm dữ liệu nhập. Các hằng số trong ví dụ slide không phải ràng buộc của tính năng.
4. Phân biệt giới hạn nghiệp vụ với giá trị nhỏ nhất/lớn nhất mà kênh UI/API cho nhập. Chỉ ghi giới hạn kênh đã có nguồn hoặc quan sát được cung cấp; đánh dấu khả năng nhập chưa xác minh. Không mở ứng dụng hay gọi API để tự khảo sát trong bước thiết kế.
5. Giữ các biến khác ở giá trị valid đã được review. Truy vết từng giá trị biên tới EC, kết quả kỳ vọng và nguồn. Gộp các điểm trùng, giải thích điểm không khả thi; không ép số lượng ca cố định hoặc đúng chín ca cho mỗi lớp.

Đầu ra: bảng `BVA-ID | Biến/EC | LB/UB và đóng/mở | Bước lân cận | Giá trị dưới/tại/trên biên | Kết quả kỳ vọng và nguồn | Khả năng nhập/điểm chưa rõ`.

**Dừng để sinh viên review Bước 4.**

## Bước 5 — Tổng hợp và thiết kế test case

1. Tổng hợp ca EP và ca BVA đã được review. Gộp ca trùng dữ liệu, tiền điều kiện và kỳ vọng, đồng thời giữ toàn bộ EC-ID/BVA-ID được phủ. Nêu lý do cho các ca bổ sung thay vì thêm tùy ý.
2. Mỗi ca phải có dữ liệu cụ thể, tiền điều kiện, các bước có thể thực hiện và expected result dẫn nguồn. Nếu thiếu oracle hoặc dữ liệu nền, để ca ở nhóm `Chờ xác nhận`; ghi phần còn thiếu, không bịa phản hồi để hoàn thiện bảng.
3. Lập bảng truy vết yêu cầu → EC/biên → TC; nêu lớp/biên chưa phủ và giới hạn còn lại. Phân biệt số ca đã thiết kế với số ca đã chạy.

Đầu ra: bảng `TC-ID | Mục tiêu | Nguồn yêu cầu | EC-ID/BVA-ID | Tiền điều kiện | Dữ liệu | Bước thực hiện | Expected Result | Actual Result | Verdict | Điểm cần xác nhận` và bảng coverage thiết kế.

Giữ `Actual Result` và `Verdict` trống; nếu cần cột trạng thái riêng, ghi `Chưa thực thi`. Không điền PASS/FAIL, kết quả thực tế, ảnh, log thực thi hoặc issue URL. Hoàn tất thiết kế không đồng nghĩa đã hoàn tất kiểm thử.

**Dừng để sinh viên review Bước 5; không tự chuyển sang thực thi.**

## Ranh giới bằng chứng và nhật ký

- Không chạy SUT, viết/chạy script kiểm thử, tạo/sửa dữ liệu kiểm thử trong ứng dụng, viết mô tả bug report, tạo issue hoặc commit trong workflow thiết kế này. Bug report do sinh viên tự viết. Yêu cầu thực thi sau này là nhiệm vụ riêng cần phạm vi rõ ràng.
- Tách thông tin kiểm thử cũ khỏi thiết kế/kết quả mới. Không nhận kết quả AI chạy thành sinh viên chạy, không chuyển ảnh/timestamp cũ thành bằng chứng mới, không dựng AI gap từ lỗi đã biết. Chỉ ghi gap khi có đối chiếu bản AI ban đầu và phần bỏ sót thực sự được xác minh.
- Dùng [nhật ký hiện tại](../../Appendix_A_Prompt_Log.md) nếu đang làm HW02; ở dự án khác dùng file log người dùng chỉ định. Theo format đang có: `## Tương tác N`, `- Công cụ`, `- Thời gian`, `### Prompt — nguyên văn`, các mục `### Output ...` theo thứ tự. Giữ nguyên nội dung các khối prompt/output, kể cả Markdown và xuống dòng; chỉ chuẩn hóa phần trình bày bên ngoài khối khi được yêu cầu.
- Lấy prompt, phản hồi hiển thị và timestamp thực từ bản ghi chat khi có thể, gồm thông báo tiến độ và phản hồi cuối. Hiển thị giờ theo múi giờ người dùng; nếu không lấy được giờ gửi gốc, ghi rõ thời điểm thu thập thay vì gán giờ ước lượng. Lưu bản đầu trước khi sinh viên chỉnh sửa; không thay log đầy đủ bằng tóm tắt. Nếu không đọc được phản hồi cuối sau khi gửi, báo giới hạn, không tuyên bố đã sao chép.

