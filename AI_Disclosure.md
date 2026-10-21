# Khai báo sử dụng AI — HW02

## 1. Thông tin môn học và sinh viên

| Mục | Giá trị |
|---|---|
| Môn học | CSC13003 — Kiểm chứng phần mềm |
| Mã bài tập | HW#02 |
| Tên bài tập | Domain Testing on EShop |
| Họ tên | Nguyễn Ngọc Đại |
| MSSV | 23120226 |
| Lớp / Khoá | 23KTPM |
| Ngày lập khai báo | 21/10/2026 |
| Chính sách AI của HW02 | Open; bắt buộc khai báo và AI Audit Report; mức Bloom-AI G9.2 và G9.3 |
| Mức sử dụng theo thang AI-01 | Cấp 4 — AI hỗ trợ sản xuất, mô tả cách sử dụng thực tế |

## 2. Nội dung khai báo

### 2.1. Công cụ AI đã dùng

OpenAI Codex trong chat hướng dẫn và chat làm bài. Không khai báo công cụ AI khác; không suy đoán tên model khi không có thông tin xác nhận.

### 2.2. Các giai đoạn có dùng AI

- [x] Lập kế hoạch và dàn ý.
- [x] Soạn nháp, phản hồi và chỉnh sửa tài liệu.
- [x] Phân tích SRS, thiết kế EP/BVA và testcase.
- [x] Viết công cụ hỗ trợ ghi log, tổng hợp và kiểm tra tài liệu.
- [x] Hướng dẫn thực thi và đối chiếu kết quả (người làm bài trực tiếp thực thi kiểm thử).
- [ ] Thiết kế đồ hoạ hoặc tạo ảnh minh chứng bằng AI.

### 2.3. Prompt chính — nguyên văn

**Prompt 1:** review và tinh giản FR-06; OpenAI Codex, 20/10/2026, 19:25, [tương tác 7](Appendix_A_Prompt_Log.md#tương-tác-7).

```text
review lại fr-06, rút gọn bộ testcase theo đúng ba yêu cầu của fr-06 và phương pháp domain testing bva, giữ các ca cần thiết để phủ các lớp và biên, gộp ca trùng mục tiêu, bỏ các biến thể không bổ sung độ phủ đáng kể hoặc thiếu căn cứ để đánh giá, không cố giữ đủ 16 ca và không ép số ca cố định, trình bày một bảng testcase gọn để tôi tự test, giữ đúng các kết quả đã ghi nhận và ghi chú d04, cập nhật đồng bộ phân lớp, đại diện, bva, truy vết và số ca, giải thích ngắn những ca đã gộp hoặc bỏ, không thêm yêu cầu gui ngoài fr-06, không sửa srs hay mã nguồn sut, chưa chạy test hay commit, log lượt này như hiện tại
```

**Prompt 2:** thiết kế Hồ sơ Mobile; OpenAI Codex, 21/10/2026, 08:29, [tương tác 21](Appendix_A_Prompt_Log.md#tương-tác-21).

```text
dùng skill domain-tesing-bva làm fr20 mục hồ sơ mobile, đọc srs fr04 và fr20 hồ sơ, viết yêu cầu, input/output và ep vào report, giải thích từng bước ra khung chat, chưa chạy test, log đầy đủ prompt này
```

Prompt, phản hồi và thời gian của các lượt được lưu trong [Appendix_A_Prompt_Log.md](Appendix_A_Prompt_Log.md). Hai prompt trên là ví dụ chính, không phải toàn bộ phạm vi dùng AI.

### 2.4. Đóng góp cụ thể của AI

| Phần | Đóng góp |
|---|---|
| Skill | Hỗ trợ tạo `skills/domain-testing-bva/SKILL.md`, quy trình và quy tắc ghi log. |
| Thiết kế bốn FR | Hỗ trợ xác định yêu cầu, input/output, EP, đại diện, BVA, testcase và truy vết cho FR-06, FR-07, FR-14, FR-20 — Hồ sơ Mobile. |
| Thực thi và kết quả | Người làm bài trực tiếp thực thi toàn bộ testcase trên Web, Admin, Android và chụp ảnh minh chứng. Codex hỗ trợ hướng dẫn tự test, giải thích quan sát và trình bày bảng kết quả; không trực tiếp thực thi hay tạo kết quả thay người làm bài. |
| Bug report và Issues | Codex từng soạn mẫu mô tả issue. Sau đó, người làm bài cung cấp bản Bug_Report.md để AI review, chỉnh mã testcase, nội dung và liên kết ảnh/issue. Không khai báo phần này là hoàn toàn không có hỗ trợ AI. |
| Hồ sơ nộp | Hỗ trợ soạn và chỉnh Report.md, README.md, các bản AI Critique/Audit và khai báo này; tổng hợp số liệu và kiểm tra liên kết. Điểm tự chấm 100/100 trong README được điền theo yêu cầu của người làm bài. |
| Cấu hình chạy | Hỗ trợ cài dependencies, chạy các dịch vụ và đổi URL API Mobile sang `192.168.137.1` theo yêu cầu để kết nối thiết bị; không sửa logic nghiệp vụ SUT. |

### 2.5. Cách rà soát, chỉnh sửa và xác minh

Người làm bài đối chiếu đầu ra với SRS và slide S04, yêu cầu bỏ kiểm tra GUI ngoài phạm vi FR-06, gộp ca trùng và không tự đặt cận trên hay quy tắc chưa được đặc tả. Các ca được tự kiểm thử trên Web, Admin và Android; ảnh minh chứng do người làm bài trực tiếp chụp. Với Hồ sơ Mobile, bước đăng xuất/đăng nhập lại được dùng để phân biệt dữ liệu trên màn hình với dữ liệu đã lưu. Hai ca F20-D08/D09 giữ NOT RUN vì chưa có đường thực thi qua giao diện tương ứng.

AI hỗ trợ review và soạn thảo các phần trên. Toàn bộ kết quả thực thi và ảnh minh chứng do người làm bài trực tiếp thực hiện trên hệ thống thật.

Chi tiết đánh giá và phần sửa được trình bày trong [AI_Critique_and_Audit.md](AI_Critique_and_Audit.md); testcase, kết quả và ảnh được dẫn trong [Report.md](Report.md), lỗi được tổng hợp trong [Bug_Report.md](Bug_Report.md).

### 2.6. Trích dẫn công cụ

[1] OpenAI, “Codex,” công cụ AI hỗ trợ thực hiện HW02, sử dụng trong các phiên chat ngày 20–21/10/2026. Nội dung tương tác được lưu tại Appendix_A_Prompt_Log.md.

## 3. Cam đoan trung thực

Bằng việc ký tên dưới đây, người làm bài xác nhận nội dung khai báo là chính xác và đầy đủ, đồng thời chịu trách nhiệm về sản phẩm nộp. Việc có hỗ trợ AI không thay thế trách nhiệm rà soát và kiểm chứng kết quả.

## 4. Xác nhận

| Mục | Giá trị |
|---|---|
| Họ tên | NGUYỄN NGỌC ĐẠI |
| MSSV | 23120226 |
| Lớp / Khoá | 23KTPM |
| Ngày lập khai báo | 21/10/2026 |
| Chữ ký sinh viên | NGUYỄN NGỌC ĐẠI |

Nội dung được điền theo cấu trúc mẫu AI-03 — FIT@HCMUS, AI Disclosure Form (2026 v1.0), được điều chỉnh từ Med Kharbach (2026), CC BY-NC-SA 4.0.
