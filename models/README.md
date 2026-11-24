# Cách đưa mô hình lên web

Bạn có một file `.html` đã làm xong. Làm 2 bước dưới đây trên trang GitHub.

## Bước 1 — Tải file lên

1. Đổi tên file trên máy: **chữ thường, không dấu, không khoảng trắng**, dùng dấu `-` thay khoảng trắng.
   Ví dụ: `Định lý Pytago.html` → `dinh-ly-pytago.html`
2. Mở thư mục **models** (chính là thư mục bạn đang xem).
3. Bấm **Add file** → **Upload files** → kéo file vào → bấm **Commit changes**.

## Bước 2 — Thêm thẻ trên trang chủ

1. Trong thư mục **models**, bấm vào file **models.json** → bấm biểu tượng ✏️.
2. Kéo xuống cuối file. Ngay sau dấu `}` cuối cùng, gõ thêm dấu phẩy `,`
3. Xuống dòng, dán đoạn này vào, rồi sửa chữ trong ngoặc kép:

```json
  {
    "file": "dinh-ly-pytago.html",
    "title": "Định lý Pytago",
    "text": "Một câu giới thiệu cho học sinh.",
    "tag": "Toán 8",
    "new": true
  }
```

4. Bấm **Commit changes…** → **Commit changes**.

Chờ 1–2 phút rồi mở trang chủ: thẻ mới đã xuất hiện.

Cuối file đúng sẽ trông như sau (phẩy **giữa** hai khối, **không** phẩy sau khối cuối):

```json
  },
  {
    "file": "dinh-ly-pytago.html",
    ...
    "new": true
  }
]
```

## Ý nghĩa từng dòng

| Dòng | Ý nghĩa |
|---|---|
| `"file"` | Tên file bạn đã tải lên ở Bước 1 — phải **giống y hệt** |
| `"title"` | Tên hiện trên thẻ |
| `"text"` | Một câu mô tả |
| `"tag"` | Nhãn nhỏ, ví dụ `Toán 8`, `Luyện tập` |
| `"new": true` | Tô màu tím cho nhãn. Không muốn thì xóa dòng này (và dấu phẩy ở dòng trên nó) |

**Muốn thẻ có ảnh riêng?** Tải ảnh vào thư mục **models/art**, rồi thêm dòng
`"image": "art/ten-anh.png",` vào khối của bạn. Không có ảnh thì web dùng hình mặc định.

## Lời khuyên để làm đúng

- Tên file: **chữ thường, không dấu, không khoảng trắng**. Tên trong `"file"` phải khớp từng chữ.
- Luôn dùng ngoặc kép thẳng `"`. **Không** dùng `“ ”` (hay tự sinh khi chép từ Word, Zalo).
- Giữa hai khối `}` và `{` phải có **một dấu phẩy**. Sau khối cuối cùng **không** có phẩy.
- Mỗi dòng bên trong khối kết thúc bằng phẩy, **trừ dòng cuối** trước `}`.
- Không xóa dấu `[` ở đầu file và `]` ở cuối file.
- Không sửa khối của mô hình khác nếu không chắc chắn.
- Nếu file `.html` dùng ảnh riêng, nhớ tải các ảnh đó lên cùng thư mục **models**.
- Muốn đổi thứ tự thẻ: cắt cả khối `{ … }` dán sang chỗ khác, giữ đúng dấu phẩy.
- Muốn ẩn một mô hình: xóa khối của nó trong `models.json` (không cần xóa file `.html`).

## Nếu trang chủ hiện khung đỏ

Đừng lo, web vẫn chạy. Khung đỏ cho biết sai ở đâu:

| Khung đỏ ghi | Cách sửa |
|---|---|
| *models.json đang bị lỗi* | Kiểm tra lại dấu phẩy và ngoặc kép, so với ví dụ ở trên |
| *Mục số N … "file" phải là tên file .html…* | Tên file ở khối thứ N có dấu, khoảng trắng hoặc gõ sai |
| *Mục số N … thiếu "title"* | Khối thứ N thiếu dòng `"title"` |
| Bấm vào thẻ thì báo **404** | `"file"` không khớp tên file đã tải lên (chữ hoa/thường cũng tính) |

Sửa sai lúc nào cũng được: mở lại `models.json` → ✏️ → sửa → **Commit changes**.
