**Khoa Công nghệ Thông tin (FIT) – Trường Đại học Khoa học Tự nhiên (HCMUS)**

**CS423 / CSC13003 – Kiểm chứng Phần mềm (AI-augmented · 2026)**

**CHÍNH SÁCH AI · BIỂU MẪU — 2026 v1.0**

# **AI Audit Report — Mẫu 5 mục cho mỗi Artifact**

*Phụ lục bắt buộc đính kèm cho mọi bài tập có dùng AI (HW#01–HW#06, Seminar).*  
*Tài liệu được biên soạn lại từ Med Kharbach, PhD (2026) — Mẫu Chính sách Sử dụng AI cho Giáo dục Đại học. Giấy phép CC BY-NC-SA 4.0. Phiên bản này được FIT@HCMUS điều chỉnh cho môn CS423 / CSC13003 Kiểm chứng Phần mềm.*

---

## **1. Thông tin Sinh viên**

| Mục | Giá trị |
| :--- | :--- |
| **Họ tên sinh viên (in hoa):** | NGUYỄN NGỌC ĐẠI |
| **MSSV:** | 23120226 |
| **Lớp / Khoá:** | 23KTPM |
| **Mã bài tập (ví dụ HW#00, HW#02):** | HW#02 |
| **Ngày làm bài:** | 2026-10-21 |
| **Công cụ AI đã dùng:** | OpenAI Codex |
| **Có dùng AI không:** | [x] Có  [ ] Không |

---

## **2. AI Critique — 200–300 từ**

Trong HW#02, OpenAI Codex hỗ trợ chuyển SRS thành bảng phân lớp tương đương (EP), xác định giá trị biên (BVA) và dựng testcase có truy vết. Quá trình cộng tác cho thấy ba điểm cần kiểm tra.

Thứ nhất, bản thiết kế FR-06 ban đầu lồng ghép yêu cầu GUI từ FR-21–FR-24 như màu nút, thẻ h1, breadcrumb và tab order. Các kiểm tra này làm rộng phạm vi đang chọn. Tôi yêu cầu tập trung lại vào thông tin sản phẩm, số lượng và phản hồi thêm giỏ.

Thứ hai, khi đặc tả thiếu quy tắc, cần phân biệt ca chưa có oracle với ca vẫn quan sát được hành vi. Bộ FR-06 được rút gọn theo độ phủ; số âm và ô rỗng được giữ theo yêu cầu review. Số âm cùng lớp với 0, không tạo lớp hoặc biên mới. Việc bổ sung dữ liệu thử không đồng nghĩa mọi biến thể đều là yêu cầu bắt buộc.

Thứ ba, hướng dẫn xác nhận lưu Hồ sơ Mobile ban đầu chưa đủ mạnh: rời rồi mở lại màn hình trong cùng phiên vẫn có thể thấy dữ liệu chưa được lưu. Sau đối chiếu thực thi, quy trình bổ sung đăng xuất và đăng nhập lại. D08/D09 vẫn NOT RUN vì chưa tạo được yêu cầu đổi role hoặc sửa hồ sơ người khác qua giao diện.

Giới hạn nằm ở việc chưa đối chiếu đủ trạng thái runtime khi thiết kế. Codex có chạy tham khảo; tôi tự kiểm thử lại và cung cấp ảnh. Bài học là dùng AI hỗ trợ cấu trúc, giữ SRS làm oracle và kiểm chứng dữ liệu trước khi kết luận.

---

## **3. Bảng Audit — 1 hàng / artifact**

Đánh giá các phiên bản đầu ra được dẫn; không xem kết quả kiểm thử chưa điền trong bước thiết kế là thiếu sót. Prompt/output nguyên văn và timestamp được dẫn bằng liên kết tới log, không cắt ghép thành lời trích dẫn. Phần điều chỉnh chính được **in đậm**. Nhãn VALID/INVALID/INCOMPLETE là nhãn audit, không phải verdict của SUT.

| (1) Prompt + Công cụ | (2) Output AI | (3) Verdict | (4) Căn cứ | (5) Phần giữ hoặc sửa sau review |
| :--- | :--- | :--- | :--- | :--- |
| **Artifact #1**<br>OpenAI Codex<br>20-10-2026 15:54<br>[Tương tác 2](Appendix_A_Prompt_Log.md#tương-tác-2) | Skill có quy trình input/output, EP, đại diện, BVA và tổng hợp testcase, kèm điểm dừng review. | **VALID** | Slide S04, trang 3–6, 8–11, 21–27: đúng trình tự; tổng hợp ca không làm thay đổi bốn bước kỹ thuật cốt lõi. | **Giữ cấu trúc skill** trong [SKILL.md](skills/domain-testing-bva/SKILL.md), dùng làm khung cho bốn FR. |
| **Artifact #2**<br>OpenAI Codex<br>20-10-2026 16:15–16:25<br>[Tương tác 3](Appendix_A_Prompt_Log.md#tương-tác-3), [4](Appendix_A_Prompt_Log.md#tương-tác-4) | Bản đầu FR-06 có 17 ca, gồm kiểm tra GUI chung từ FR-21–FR-24. | **INCOMPLETE** | SRS FR-06 và phạm vi đã chọn: có phần thiết kế nghiệp vụ dùng được, nhưng lẫn các mục tiêu GUI ngoài phạm vi. Không kết luận cả 17 ca đều sai. | **Bỏ phần GUI ngoài phạm vi, gộp ca trùng và giữ mục tiêu cốt lõi**, theo [tương tác 5](Appendix_A_Prompt_Log.md#tương-tác-5), [7](Appendix_A_Prompt_Log.md#tương-tác-7). |
| **Artifact #3**<br>OpenAI Codex<br>20-10-2026 19:25–19:40<br>[Tương tác 7](Appendix_A_Prompt_Log.md#tương-tác-7), [8](Appendix_A_Prompt_Log.md#tương-tác-8) | Sau rút gọn còn 6 ca; -1 được gộp độ phủ vào 0, các ca khám phá bị bỏ khỏi bộ chính. | **INCOMPLETE** | SRS FR-06, S04: -1 và 0 cùng lớp số nguyên không dương. Ô rỗng được giữ để quan sát riêng; không tự đặt required/default. REP là mã đại diện. | Theo yêu cầu review, **giữ lại D05 với -1 và bổ sung ô rỗng, sau đó mang mã D08**. Bộ cuối 8 ca. `REP06-N` không tạo lớp/biên mới; D04=0 và D06=1.5 có căn cứ từ miền nguyên dương. |
| **Artifact #4**<br>OpenAI Codex<br>20-10-2026 22:43–22:50<br>[Tương tác 10](Appendix_A_Prompt_Log.md#tương-tác-10), [11](Appendix_A_Prompt_Log.md#tương-tác-11) | Yêu cầu/input-output và EP cho FR-07, gồm trạng thái giỏ, quan hệ sản phẩm đã có/chưa có và thao tác xóa. | **VALID** | SRS FR-07, S04: log đã nhận diện thêm trùng và trạng thái xác nhận xóa; không có căn cứ nói hai nội dung này bị bỏ sót. | **Giữ các mục tiêu EP**. Mã hiện hành cho thêm trùng là `E07-P1`, `E07-ODUPLICATE`; xóa là `E07-DEL`, `E07-D0/D1`. Không thêm kỳ vọng xử lý hủy xóa ngoài đặc tả. |
| **Artifact #5**<br>OpenAI Codex<br>20-10-2026 23:02–23:26<br>[Tương tác 12](Appendix_A_Prompt_Log.md#tương-tác-12), [13](Appendix_A_Prompt_Log.md#tương-tác-13), [14](Appendix_A_Prompt_Log.md#tương-tác-14) | Bộ 9 testcase FR-07, kết hợp EP/BVA, có dữ liệu, tiền điều kiện, expected và truy vết; kết quả thực thi để trống. | **VALID** | SRS FR-07, S04: BVA xét số dòng 0–1–2; giỏ có hàng dùng làm tiền điều kiện. D01 là giỏ trống, D04 là nhãn “Tổng cộng”, D09 là điều hướng tiếp tục mua sắm. | **Giữ cấu trúc 9 ca**; điền actual, verdict và ảnh sau thực thi. Không thêm yêu cầu định dạng VND hoặc yêu cầu tạo tài khoản mới cho D09. |
| **Artifact #6**<br>OpenAI Codex<br>21-10-2026 00:34–01:06<br>[Tương tác 15](Appendix_A_Prompt_Log.md#tương-tác-15), [16](Appendix_A_Prompt_Log.md#tương-tác-16), [17](Appendix_A_Prompt_Log.md#tương-tác-17), [18](Appendix_A_Prompt_Log.md#tương-tác-18) | Thiết kế FR-14 ban đầu gồm lớp danh sách trống và đầu ra tương ứng; các đại diện dự kiến chỉ ánh xạ 8/10 lớp. | **INCOMPLETE** | SRS FR-14: Thêm/Xem/Xóa, tên không rỗng; chưa quy định hành vi riêng cho danh sách trống hoặc cấm tên trùng. Việc giữ lớp chưa thực hiện cần nhất quán với phạm vi cuối. | **Gộp việc xem danh sách vào fixture đã chuẩn bị**, bỏ kỳ vọng riêng không được đặc tả; bộ hiện hành có 8 lớp. Không coi tên trùng là invalid, không tự thêm Sửa hoặc max 255. |
| **Artifact #7**<br>OpenAI Codex<br>21-10-2026 01:15–01:22<br>[Tương tác 19](Appendix_A_Prompt_Log.md#tương-tác-19), [20](Appendix_A_Prompt_Log.md#tương-tác-20) | BVA độ dài tên với 0, 1, 2 ký tự; bộ 4 testcase FR-14. | **VALID** | SRS FR-14 và S04: cận dưới đóng 1 ký tự, xét LB−1/LB/LB+1 là rỗng/A/AB; không đặt cận trên. | **Giữ ba điểm 0–1–2** và bốn ca tên rỗng, A, AB, xóa danh mục thử; dùng chung kiểm tra Xem. Không mô tả bộ này chỉ gồm hai điểm 0 và 1. |
| **Artifact #8**<br>OpenAI Codex<br>21-10-2026 08:45<br>[Tương tác 23](Appendix_A_Prompt_Log.md#tương-tác-23) | Bước kiểm tra lưu FR-20 ban đầu dùng rời rồi mở lại Hồ sơ, tiền điều kiện đòi P0 hợp lệ. Bản thiết kế được giữ ở commit `080760f`. | **INCOMPLETE** | SRS FR-04/20: cần đối chiếu dữ liệu thực sự đã lưu. Bản đầu [F20-D01](https://github.com/nndai/HW02_AI_DomainTesting/blob/080760ffc4681305a7427a0f2bc2a03af7ecb0b4/Report.md#L611) chưa loại được ảnh hưởng trạng thái cùng phiên. | **Đóng bàn phím trước Cập nhật, đăng xuất/đăng nhập lại để kiểm tra lưu; P0 lấy theo dữ liệu thực tế**, có thể rỗng/null. Giữ biên điện thoại 9–10–11–12. |
| **Artifact #9**<br>OpenAI Codex<br>21-10-2026 08:45<br>[Tương tác 23](Appendix_A_Prompt_Log.md#tương-tác-23) | D08 tự đổi role và D09 sửa hồ sơ người khác được thiết kế có điều kiện; chưa khẳng định có đường thao tác Mobile. | **VALID** | SRS FR-04/20: quy tắc quyền có căn cứ, nhưng thiết kế không đồng nghĩa đã thực thi. Bản đầu đã nêu rõ tiền điều kiện còn phụ thuộc khả năng tương tác. | **Giữ ca thiết kế; cập nhật D08/D09 thành NOT RUN** sau khảo sát UI. Không gọi hai ca này là avatar/đổi mật khẩu và không kết luận PASS vì dữ liệu chưa thay đổi. |
| **Artifact #10**<br>OpenAI Codex<br>21-10-2026 10:19–10:22<br>[Tương tác 24](Appendix_A_Prompt_Log.md#tương-tác-24), [25](Appendix_A_Prompt_Log.md#tương-tác-25) | Tổng kết và Mandatory Disclosure của Report; lượt này chưa hoàn thiện Bug_Report/Issues. Số liệu testcase đã được tổng hợp đúng. | **INCOMPLETE** | HW02 mục 9, 14 và AI-01 mục 8: khai báo còn phủ nhận việc AI trực tiếp chạy, chưa phân biệt các lượt tham khảo với lần tự kiểm thử lại. | **Giữ số liệu 30 thiết kế, 28 đã chạy, 8 PASS, 20 FAIL, 2 NOT RUN; sửa khai báo về các lượt AI chạy tham khảo.** Mười bug được đối chiếu riêng trong [Bug_Report.md](Bug_Report.md), không gán thành đầu ra của tương tác 24–25. |

---

## **4. Tổng kết Độ chính xác AI**

| Chỉ số | Số lượng | Tỉ lệ |
| :--- | :---: | :---: |
| **Tổng artifact đã audit ở Mục 3** | 10 | 100% |
| **VALID** | 5 | 50% |
| **INVALID** | 0 | 0% |
| **INCOMPLETE** | 5 | 50% |

Tỷ lệ chỉ mô tả 10 artifact trong bảng, không phải độ chính xác chung của công cụ hoặc toàn bộ phản hồi AI. VALID không đồng nghĩa testcase đã được chạy hay SUT không có lỗi.

---

## **5. Kết luận — Khi nào nên / không nên dùng AI?**

AI hỗ trợ rất tốt ở giai đoạn phân tích SRS, phân chia lớp tương đương (EP), xác định biên (BVA) và lập khung test case có truy vết, giúp tiết kiệm thời gian và hạn chế trùng lặp ca kiểm thử.

Tuy nhiên, không nên để AI tự quyết định phạm vi kiểm thử hay kỳ vọng khi thiếu đặc tả. AI dễ mở rộng phạm vi sang các yêu cầu giao diện chung ngoài lề, lúng túng khi SRS chưa đầy đủ và không nắm được cơ chế lưu trạng thái thực tế (như cache màn hình so với lưu backend).

Khuyến nghị: Chỉ dùng AI để dựng khung phương pháp và bảng biểu ban đầu. Người kiểm thử phải rà soát phạm vi, tự thiết kế ca khám phá khi thiếu quy tắc và trực tiếp thực thi trên hệ thống thật.

---

## **6. Mandatory Disclosure (dán nguyên văn)**

> Báo cáo, skill và bộ testcase có hỗ trợ soạn thảo của OpenAI Codex. Tôi rà soát phạm vi, yêu cầu chỉnh EP/BVA và dữ liệu thử; trực tiếp kiểm thử lại, xác nhận kết quả và cung cấp ảnh dùng trong báo cáo. Codex có các lượt chạy tham khảo riêng, hỗ trợ diễn đạt và tổng hợp tài liệu. Codex từng nháp mô tả issue, sau đó review và chỉnh bản Bug_Report.md tôi cung cấp; không khai báo phần này là hoàn toàn không dùng AI. Bản audit này cũng có hỗ trợ soạn và sửa của Codex. Chi tiết phạm vi sử dụng được ghi trong [AI_Disclosure.md](AI_Disclosure.md), các tương tác được dẫn trong [Appendix_A_Prompt_Log.md](Appendix_A_Prompt_Log.md).

---

## **Chữ ký**

| Mục | Giá trị |
| :--- | :--- |
| **Họ tên sinh viên (in hoa):** | NGUYỄN NGỌC ĐẠI |
| **MSSV:** | 23120226 |
| **Lớp / Khoá:** | 23KTPM |
| **Môn học:** | CS423 / CSC13003 – Kiểm chứng Phần mềm |
| **Giảng viên:** | ThS. Huỳnh Phước Hải |
| **Ngày:** | 2026-10-21 |
| **Chữ ký:** | Nguyễn Ngọc Đại |

---

## **Tham khảo**

* Đề HW02, mục 6, 9–10 và 14.
* Slide S04 — Domain Testing, trang 3–6, 8–11, 21–27.
* SRS trong README của EShop SUT: FR-06, FR-07, FR-14, FR-04 và FR-20.
* AI-01, mục 8, 11; mẫu AI-02 — FIT@HCMUS (2026 v1.0), điều chỉnh từ Kharbach (2026), CC BY-NC-SA 4.0.
* [Nhật ký prompt/output](Appendix_A_Prompt_Log.md), [Report.md](Report.md), [Bug_Report.md](Bug_Report.md), [AI_Disclosure.md](AI_Disclosure.md).
