# Hướng dẫn — không cần biết lập trình

## A. Cài đặt 1 lần duy nhất

| Bước | Việc cần làm |
|---|---|
| 1 | Tạo tài khoản tại **github.com** |
| 2 | Bấm **New repository** → đặt tên `AhaPi` → chọn **Public** → **Create** |
| 3 | Bấm **uploading an existing file** → kéo thả **toàn bộ** thư mục này vào → **Commit changes** |
| 4 | Vào **Settings → Pages** → mục *Branch* chọn `main` / `(root)` → **Save** |
| 5 | Đợi 2 phút. Web của bạn ở địa chỉ `https://<tên-tài-khoản>.github.io/AhaPi/` |

Gửi địa chỉ đó cho học sinh. Xong.

## B. Thêm bài mới (làm mỗi ngày/tuần)

Mở file **soan-bai.html** trên máy bằng cách nhấp đúp. Bên trái điền, bên phải xem thử ngay lập tức.

| Bước | Việc cần làm | Ở đâu |
|---|---|---|
| 1 | Điền Ngày dạy, Chương, Tên bài, dán link YouTube | soan-bai.html |
| 2 | Gõ lý thuyết, thêm từ vựng, thêm các câu bài tập kèm đáp án | soan-bai.html |
| 3 | Bấm **⬇ Tải file .md** → file lưu về máy | soan-bai.html |
| 4 | Lên GitHub, mở thư mục `lessons` → **Add file → Upload files** → thả file vừa tải → **Commit** | GitHub |
| 5 | Mở file `index.json` trên GitHub → bấm ✏️ → chọn hết → chép vào ô "Bước 6" của soan-bai.html | Cả hai |
| 6 | Bấm **Thêm bài này vào danh sách** → **Chép kết quả** → dán đè lên `index.json` trên GitHub → **Commit** | Cả hai |

Bài mới được thêm vào **cuối** danh sách. Muốn bài xuất hiện ở vị trí khác trong chương, chỉ cần cắt dòng đó dán lên trên — thứ tự dòng trong `index.json` chính là thứ tự học.

Sau 1–2 phút bài mới xuất hiện trên web.

> **Vì sao phải làm bước 5–6?** Trang web không tự biết bạn vừa thêm file. `index.json` là mục lục — có tên trong mục lục thì bài mới hiện ra ở menu.

## C. Cách gõ nội dung

| Bạn gõ | Kết quả |
|---|---|
| `## Công thức` | Tiêu đề mục |
| `### Ví dụ` | Tiêu đề nhỏ |
| `**quan trọng**` | **quan trọng** (in đậm màu tím) |
| `- mục a` | Gạch đầu dòng |
| `> ghi nhớ` | Khung ghi nhớ |
| `$x^2 + 1$` | Công thức nằm trong dòng |
| `$$\int x\,dx$$` | Công thức riêng một dòng, căn giữa |

## C1. Chèn ảnh vào bài

| Bước | Việc cần làm |
|---|---|
| 1 | Trên GitHub, mở thư mục **images** → **Add file → Upload files** → thả ảnh vào → **Commit** |
| 2 | Trong trình soạn bài, đặt con trỏ vào chỗ muốn chèn rồi bấm **🖼 Chèn ảnh** |
| 3 | Sửa `ten-file.png` thành đúng tên ảnh vừa tải lên, sửa phần trong ngoặc vuông thành chú thích |

```
![Sơ đồ tỉ số 2 : 3](images/so-do-ti-so.png)
```

Chữ trong ngoặc vuông hiện thành dòng chú thích nhỏ ngay dưới ảnh. Không muốn chú thích thì để trống: `![](images/so-do-ti-so.png)`.

Ảnh to mấy cũng tự co cho vừa khung, không cần chỉnh kích thước trước. Đặt tên file **không dấu, không khoảng trắng** — dùng gạch ngang thay khoảng trắng.

> Khung xem thử bên phải sẽ hiện ô ảnh vỡ, vì máy bạn chưa có file ảnh đó. Bình thường — lên web là hiện đúng.

## C2. Gõ từ vựng

Ô **Bước 3** trong trình soạn bài, mỗi dòng một từ:

```
ratio /ˈreɪʃiəʊ/ : tỉ số
highest common factor : ước chung lớn nhất
```

Bên trái dấu `:` là tiếng Anh, bên phải là tiếng Việt. Phần phiên âm trong hai dấu gạch chéo **không bắt buộc** — muốn có thì tra trên Oxford rồi chép vào.

Học sinh bấm nút 🔊 là nghe được phát âm, và bấm mũi tên ↗ để mở thẳng mục từ đó trên từ điển Oxford. Giọng đọc lấy từ chính máy của học sinh nên không cần mạng, không cần cài gì thêm. Máy nào không có sẵn giọng tiếng Anh thì nút loa tự ẩn đi.

Công thức hay dùng: `\frac{a}{b}` phân số · `\sqrt{x}` căn · `x^{2}` mũ · `x_{1}` chỉ số dưới · `\int` tích phân · `\sum` tổng · `\Rightarrow` suy ra · `\leq` bé hơn bằng.

**Gõ sai công thức thì sao?** Chỗ sai hiện chữ đỏ gạch chân ngay ở khung xem thử. Sửa trước khi tải file.

## D. Sửa hoặc xoá bài

| Việc | Cách làm |
|---|---|
| Sửa nội dung | Mở file trong `lessons` trên GitHub → bấm ✏️ → sửa → Commit |
| Đổi tên bài | Sửa dòng tương ứng trong `index.json` |
| Xoá bài | Xoá dòng của bài đó trong `index.json` (file `.md` để lại cũng được) |
| Đổi thứ tự bài | Kéo dòng lên/xuống trong `index.json` |
| Đổi màu nhấn của web | Sửa dòng `--nhan:` ở đầu `index.html` |

## E. Lưu ý

- Nhấp đúp `index.html` trên máy sẽ **không chạy** — trình duyệt chặn đọc file. Cứ xem qua địa chỉ GitHub Pages. Riêng `soan-bai.html` thì chạy tốt trên máy.
- Cần mạng để hiện công thức toán và video.
- Học sinh bấm **Đánh dấu đã học** thì dấu ✓ lưu ngay trên máy của em đó, không gửi đi đâu cả. Xoá lịch sử trình duyệt là mất.
- `id` của bài chính là tên file `.md`. Hai thứ này phải khớp nhau — trình soạn bài đã tự lo việc đó.
