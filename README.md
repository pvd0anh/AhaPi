# AhaPi

Web tĩnh để đăng bài giảng Toán: video YouTube, lý thuyết có công thức LaTeX, bài tập kèm đáp án ẩn. Không cần server, không cần database, không cần đăng nhập.

## Cấu trúc

| Đường dẫn | Vai trò |
|---|---|
| `index.html` | Trang học sinh xem. Đọc `index.json` rồi nạp bài từ `lessons/` |
| `soan-bai.html` | Công cụ soạn bài của giáo viên. Điền form → tải file `.md` → sinh sẵn dòng JSON |
| `index.json` | Mục lục. **Thứ tự dòng = thứ tự học** |
| `lessons/*.md` | Nội dung từng bài. Tên file phải trùng `id` trong `index.json` |
| `404.html` · `favicon.svg` | Trang lỗi và biểu tượng |
| `.nojekyll` | **Bắt buộc giữ.** Ngăn GitHub Pages xử lý file `.md`, nếu xoá thì bài giảng không tải được |
| `HUONG-DAN.md` | Hướng dẫn thao tác hằng ngày, không rành IT vẫn làm được |

## Deploy

| Bước | Thao tác |
|---|---|
| 1 | Tạo repository mới trên GitHub, đặt tên `ahapi`, chọn **Public** |
| 2 | Upload toàn bộ file trong thư mục này vào **gốc** repo (không lồng thêm thư mục) |
| 3 | **Settings → Pages** → Branch: `main`, thư mục `/ (root)` → **Save** |
| 4 | Đợi 1–2 phút. Web chạy tại `https://<tài-khoản>.github.io/<tên-repo>/` |

Nếu dùng Netlify hoặc Vercel: kéo thả thư mục này, không cần cấu hình build.

## Thêm bài mới

Mở `soan-bai.html` bằng cách nhấp đúp (chạy ngay trên máy, không cần server):

1. Điền ngày, chương, tên bài, link YouTube, lý thuyết, bài tập
2. Bấm **Tải file .md** → upload file đó vào thư mục `lessons/`
3. Dán `index.json` hiện tại vào ô Bước 5 → bấm **Thêm bài này vào danh sách** → chép kết quả đè lên `index.json`

Chi tiết từng bước kèm ảnh thao tác: xem `HUONG-DAN.md`.

## Cú pháp file bài giảng

```markdown
[[VIDEO]] https://youtu.be/xxxxxxxxxxx

## Tiêu đề mục

Chữ thường, **in đậm**, công thức trong dòng $a^2+b^2=c^2$.

$$\int_{0}^{1} x\,dx = \frac{1}{2}$$

> Khung ghi nhớ

[[BÀI TẬP]]

[[Câu]] Đề bài câu 1
[[Đáp án]] Lời giải câu 1

[[Câu]] Đề bài câu 2
[[Đáp án]] Lời giải câu 2
```

| Thẻ | Bắt buộc | Ghi chú |
|---|---|---|
| `[[VIDEO]]` | Không | Bỏ dòng này nếu bài không có video |
| `[[BÀI TẬP]]` | Không | Mọi thứ phía sau là bài tập |
| `[[Câu]]` | Không | Mỗi câu một thẻ |
| `[[Đáp án]]` | Không | Ẩn cho tới khi học sinh bấm |

## Tuỳ chỉnh

| Muốn đổi | Sửa ở đâu |
|---|---|
| Màu chủ đạo | Dòng `--nhan:` đầu `index.html` (và `--nhan-mo:` cho nền nhạt) |
| Tên web | `<title>` + `<b>AhaPi</b>` trong `index.html`, `document.title` cuối file; rồi `soan-bai.html`, `404.html` |
| Biểu tượng logo | Chữ `π` trong `index.html`, `404.html` và `favicon.svg` |
| Độ rộng cột đọc | `.trong{max-width:780px}` |
| Độ rộng sidebar | Biến `--ben` |

## Thư viện dùng qua CDN

| Thư viện | Dùng để |
|---|---|
| KaTeX 0.16.9 | Render công thức toán |
| marked 11.1.1 | Render Markdown |
| Google Fonts | Be Vietnam Pro · Source Serif 4 · IBM Plex Mono |

Cần mạng để hiển thị công thức và video. Muốn chạy hoàn toàn offline thì phải tải các thư viện này về đặt trong repo.

## Giới hạn cần biết

- Tiến độ "đã học" lưu bằng `localStorage` trên máy từng học sinh. Không đồng bộ giữa các thiết bị, giáo viên không xem được ai học tới đâu.
- Mở `index.html` bằng cách nhấp đúp trên máy sẽ báo lỗi vì trình duyệt chặn `fetch` với giao thức `file://`. Phải chạy qua GitHub Pages hoặc một server tĩnh bất kỳ.
- Video để chế độ Riêng tư trên YouTube sẽ không nhúng được.
