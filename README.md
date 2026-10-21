# HW02 — Domain Testing on EShop

- **Sinh viên:** Nguyễn Ngọc Đại — **MSSV:** 23120226.
- **Phạm vi:** FR-06 — Chi tiết sản phẩm Web; FR-07 — Giỏ hàng Web; FR-14 — Danh mục Admin; FR-20 — Hồ sơ Mobile.
- **SUT:** EShop, commit `85af3ba`.
- **GitHub Issues:** [nndai/HW02_AI_DomainTesting](https://github.com/nndai/HW02_AI_DomainTesting/issues).
- **Điểm tự đánh giá:** **100/100**.

## Tự đánh giá

| Tiêu chí | Điểm tối đa | Điểm tự chấm | Bằng chứng |
|---|---:|---:|---|
| Pool A — FR-06: Domain Testing và BVA | 25 | 25 | [Report, mục 1](Report.md#1-fr-06--chi-tiết-sản-phẩm) |
| Pool B — FR-07: Domain Testing và BVA | 25 | 25 | [Report, mục 2](Report.md#2-fr-07--giỏ-hàng) |
| Pool C — FR-14: Domain Testing và BVA | 25 | 25 | [Report, mục 3](Report.md#3-fr-14--danh-mục-admin) |
| Pool D — FR-20: Domain Testing và BVA | 15 | 15 | [Report, mục 4](Report.md#4-fr-20--hồ-sơ-mobile) |
| Agent Skills | 10 | 10 | [Skill](skills/domain-testing-bva/SKILL.md), [video demo](https://youtu.be/G_ysXCRs3t8) |
| **Tổng** | **100** | **100** | |

## Tổng kết kiểm thử

**Số tính năng: 4.** EP và BVA dùng chung bộ testcase; không cộng các ca biên đã dùng lại thành ca mới.

| Tính năng | Ca thiết kế | Đã thực thi | PASS | FAIL | NOT RUN | Bug đã báo cáo |
|---|---:|---:|---:|---:|---:|---:|
| FR-06 | 8 | 8 | 0 | 8 | 0 | 3 |
| FR-07 | 9 | 9 | 1 | 8 | 0 | 5 |
| FR-14 | 4 | 4 | 3 | 1 | 0 | 1 |
| FR-20 — Hồ sơ Mobile | 9 | 7 | 4 | 3 | 2 | 1 |
| **Tổng** | **30** | **28** | **8** | **20** | **2** | **10** |

F20-D08 và F20-D09 giữ NOT RUN vì chưa xác định được đường tạo yêu cầu tự đổi role hoặc sửa hồ sơ người khác qua giao diện Hồ sơ Mobile. Không kết luận PASS cho hai ca này. Các ca FAIL có cùng lỗi được gộp khi báo cáo bug.

## Video demo

[Demo sử dụng skill Domain Testing và BVA cho Hồ sơ Mobile — FR-20](https://youtu.be/G_ysXCRs3t8).

## Tài liệu

- [Report.md](Report.md): yêu cầu, EP, đại diện, BVA, testcase, kết quả và truy vết của bốn FR.
- [Bug_Report.md](Bug_Report.md): 10 lỗi, liên kết GitHub Issues và ảnh minh chứng.
- [AI_Critique_and_Audit.md](AI_Critique_and_Audit.md): critique và audit đầu ra AI.
- [AI_Disclosure.md](AI_Disclosure.md): khai báo sử dụng AI.
- [Appendix_A_Prompt_Log.md](Appendix_A_Prompt_Log.md): nhật ký prompt và output.
- [Skill domain-testing-bva](skills/domain-testing-bva/SKILL.md): quy trình thiết kế tái sử dụng.
- `screenshots/`: ảnh minh chứng được dẫn trong báo cáo.

