# Bug Report — EShop FR-06 & FR-07 & FR-14 & FR-20

**Sinh viên:** Nguyễn Ngọc Đại — MSSV: 23120226
**SUT:** EShop · commit `85af3ba` · Web/Admin: Windows 11, Chrome, localhost · Mobile: Android, Expo Go SDK 54
**Issues:** https://github.com/nndai/HW02_AI_DomainTesting/issues

---

## BUG-01

🔗 [#1 — [FR-06] Trang chi tiết sản phẩm không hiển thị danh mục](https://github.com/nndai/HW02_AI_DomainTesting/issues/1)

| Trường | Nội dung |
|---|---|
| **TC** | F06-D01 |
| **Yêu cầu** | R06-01 |
| **Mức độ** | Medium |
| **Tiền điều kiện** | Đăng nhập `test@eshop.com`; mở `/product/1` |
| **Bước tái hiện** | 1. Đăng nhập → 2. Mở trang chi tiết sản phẩm |
| **Kỳ vọng** | Hiển thị đủ: ảnh, tên, giá, mô tả, danh mục |
| **Thực tế** | Thiếu danh mục "Điện thoại" |
| **Bằng chứng** | [D01.png](screenshots/FR06/D01.png) |

---

## BUG-02

🔗 [#2 — [FR-06] Lần bấm Thêm vào giỏ hàng đầu tiên không thêm sản phẩm và không có phản hồi](https://github.com/nndai/HW02_AI_DomainTesting/issues/2)

| Trường | Nội dung |
|---|---|
| **TC** | F06-D02, F06-D03 |
| **Yêu cầu** | R06-02, R06-03 |
| **Mức độ** | Critical |
| **Tiền điều kiện** | Đăng nhập; giỏ trống; trang `/product/1` tải xong |
| **Bước tái hiện** | 1. Nhập `q=1` → 2. Bấm "Thêm vào giỏ" một lần → 3. Quan sát giỏ |
| **Kỳ vọng** | Sản phẩm vào giỏ; có toast hoặc badge cập nhật |
| **Thực tế** | Không toast, không badge, giỏ trống. Lặp với `q=2` — kết quả tương tự |
| **Bằng chứng** | [D02-1.png](screenshots/FR06/D02-1.png) · [D02-2.png](screenshots/FR06/D02-2.png) |

---

## BUG-03

🔗 [#3 — [FR-06] Bấm Thêm hai lần có thể đưa số lượng 0, số âm hoặc NaN vào giỏ hàng](https://github.com/nndai/HW02_AI_DomainTesting/issues/3)

| Trường | Nội dung |
|---|---|
| **TC** | F06-D04, F06-D05, F06-D08 (lần bấm thứ hai) |
| **Yêu cầu** | R06-02 — số lượng phải là số nguyên ≥ 1 |
| **Mức độ** | High |
| **Tiền điều kiện** | Đăng nhập; giỏ trống; trang `/product/1` tải xong |
| **Bước tái hiện** | 1. Nhập `0` / `-1` / xóa trống → 2. Bấm "Thêm" **hai lần** → 3. Mở giỏ |
| **Kỳ vọng** | Không thêm sản phẩm với số lượng không hợp lệ |
| **Thực tế** | Giỏ chứa `q=0`, `q=-1` hoặc hiển thị **NaN** |
| **Bằng chứng** | [D04-3.png](screenshots/FR06/D04-3.png) · [D08-3.png](screenshots/FR06/D08-3.png) |

---

## BUG-04

🔗 [#4 — [FR-07] Giỏ trống thiếu hình minh họa](https://github.com/nndai/HW02_AI_DomainTesting/issues/4)

| Trường | Nội dung |
|---|---|
| **TC** | F07-D01 |
| **Yêu cầu** | R07-06 — giỏ trống phải có hình minh họa và thông báo rõ ràng |
| **Mức độ** | Low |
| **Tiền điều kiện** | Đăng nhập; giỏ hàng trống |
| **Bước tái hiện** | 1. Đăng nhập → 2. Mở trang giỏ hàng khi chưa có sản phẩm |
| **Kỳ vọng** | Hiển thị hình minh họa và thông báo giỏ trống |
| **Thực tế** | Thiếu hình minh họa |
| **Bằng chứng** | [D01.png](screenshots/FR07/D01.png) |

---

## BUG-05

🔗 [#5 — [FR-07] Không có nút tăng và giảm số lượng](https://github.com/nndai/HW02_AI_DomainTesting/issues/5)

| Trường | Nội dung |
|---|---|
| **TC** | F07-D02, F07-D03, F07-D05, F07-D06 |
| **Yêu cầu** | R07-01 — có nút +/− để chỉnh số lượng |
| **Mức độ** | High |
| **Tiền điều kiện** | Đăng nhập; giỏ có ≥ 1 sản phẩm |
| **Bước tái hiện** | 1. Thêm sản phẩm → 2. Mở giỏ hàng → 3. Quan sát nút +/− |
| **Kỳ vọng** | Có nút + và − để chỉnh số lượng từng dòng |
| **Thực tế** | Không có nút +/− |
| **Bằng chứng** | [D02.png](screenshots/FR07/D02.png) · [D03.png](screenshots/FR07/D03.png) · [D05.png](screenshots/FR07/D05.png) · [D06.png](screenshots/FR07/D06.png) |

---

## BUG-06

🔗 [#6 — [FR-07] Nhãn tổng tiền hiển thị sai yêu cầu](https://github.com/nndai/HW02_AI_DomainTesting/issues/6)

| Trường | Nội dung |
|---|---|
| **TC** | F07-D04 |
| **Yêu cầu** | R07-05 — nhãn phải là "Tổng cộng" |
| **Mức độ** | Low |
| **Tiền điều kiện** | Đăng nhập; giỏ có ≥ 1 sản phẩm |
| **Bước tái hiện** | 1. Thêm sản phẩm → 2. Mở giỏ → 3. Đọc nhãn tổng tiền |
| **Kỳ vọng** | Nhãn hiển thị "Tổng cộng" |
| **Thực tế** | Hiển thị "Tổng tạm tính" ở cả giỏ một dòng và hai dòng |
| **Bằng chứng** | [Giỏ một dòng](screenshots/FR07/D04-2.png) · [Giỏ hai dòng](screenshots/FR07/D04-1.png) |

---

## BUG-07

🔗 [#7 — [FR-07] Thêm trùng sản phẩm tạo dòng mới](https://github.com/nndai/HW02_AI_DomainTesting/issues/7)

| Trường | Nội dung |
|---|---|
| **TC** | F07-D07 |
| **Yêu cầu** | R07-02 — thêm cùng sản phẩm phải tăng số lượng, không tạo dòng mới |
| **Mức độ** | High |
| **Tiền điều kiện** | Đăng nhập; giỏ có iPhone 15 Pro Max (P), số lượng 3 và Samsung Galaxy S24 Ultra (Q), số lượng 2 |
| **Bước tái hiện** | 1. Thêm lại P với số lượng 2 (bấm hai lần do lỗi FR-06 đã biết) → 2. Mở giỏ, đối chiếu các dòng của P |
| **Kỳ vọng** | Số lượng P tăng trên dòng hiện có; P vẫn chỉ có một dòng |
| **Thực tế** | P xuất hiện thành hai dòng có số lượng 3 và 2; Q vẫn có số lượng 2 |
| **Bằng chứng** | [D07.png](screenshots/FR07/D07.png) |

---

## BUG-08

🔗 [#8 — [FR-07] Xóa sản phẩm không có dialog xác nhận](https://github.com/nndai/HW02_AI_DomainTesting/issues/8)

| Trường | Nội dung |
|---|---|
| **TC** | F07-D08 |
| **Yêu cầu** | R07-03 — xóa phải có dialog xác nhận |
| **Mức độ** | Medium |
| **Tiền điều kiện** | Đăng nhập; giỏ có ≥ 1 sản phẩm |
| **Bước tái hiện** | 1. Thêm sản phẩm → 2. Bấm "Xóa" trên dòng sản phẩm |
| **Kỳ vọng** | Hiển thị dialog xác nhận trước khi xóa |
| **Thực tế** | Xóa ngay lập tức, không có dialog |
| **Bằng chứng** | [D08.png](screenshots/FR07/D08.png) |

---

## BUG-09

🔗 [#9 — FR-14: Vẫn tạo được danh mục khi tên rỗng](https://github.com/nndai/HW02_AI_DomainTesting/issues/9)

| Trường | Nội dung |
|---|---|
| **TC** | F14-D01 |
| **Yêu cầu** | R14-04 — tên danh mục không được rỗng |
| **Mức độ** | High |
| **Tiền điều kiện** | Đăng nhập tài khoản admin; vào trang quản lý danh mục |
| **Bước tái hiện** | 1. Chọn Tạo mới danh mục → 2. Để trống trường tên → 3. Xác nhận tạo |
| **Kỳ vọng** | Không tạo danh mục có tên rỗng |
| **Thực tế** | Tạo danh mục ID 4 có tên rỗng; danh sách tăng từ 3 lên 4 dòng |
| **Bằng chứng** | [D01.png](screenshots/FR14/D01.png) |

---

## BUG-10

🔗 [#10 — FR-20: Kiểm tra số điện thoại không đúng SRS](https://github.com/nndai/HW02_AI_DomainTesting/issues/10)

| Trường | Nội dung |
|---|---|
| **TC** | F20-D01, F20-D02, F20-D05 |
| **Yêu cầu** | R20-03 — số điện thoại gồm toàn chữ số, bắt đầu bằng `0`, dài 10–11 chữ số (quy tắc FR-04 áp dụng cho Hồ sơ Mobile FR-20) |
| **Mức độ** | Medium |
| **Tiền điều kiện** | Đăng nhập Mobile bằng `test@eshop.com`; mở Hồ sơ |
| **Bước tái hiện** | 1. Lần lượt thử riêng `0912345678`, `09123456789`, `1912345678`, các trường đi kèm theo từng testcase → 2. Đóng bàn phím, bấm Cập nhật → 3. Đăng xuất/đăng nhập lại để đối chiếu số đã lưu |
| **Kỳ vọng** | Chấp nhận `0912345678` và `09123456789`; không lưu `1912345678` |
| **Thực tế** | Hai số hợp lệ bắt đầu bằng `0` bị từ chối với lỗi yêu cầu 9–10 chữ số. `1912345678` được báo cập nhật thành công và vẫn được lưu sau đăng nhập lại |
| **Bằng chứng** | [D01.jpg](screenshots/FR20/D01.jpg) · [D02.jpg](screenshots/FR20/D02.jpg) · [D05.jpg](screenshots/FR20/D05.jpg) |