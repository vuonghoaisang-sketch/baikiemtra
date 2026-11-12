# Quản lý sách

Ứng dụng Flask quản lý danh mục và sách, dùng SQLite để lưu dữ liệu. Dashboard hiển thị hai tổng và bảng số sách theo danh mục. Mỗi đầu sách được tính một lần, kể cả khi số lượng tồn bằng 0.

## Chạy trên Windows PowerShell

Mở PowerShell tại thư mục `KiemTra-QuanLySach`. Dùng Python 3.10 trở lên.

```powershell
python --version
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe init_db.py
.\.venv\Scripts\python.exe app.py
```

Mở [http://127.0.0.1:5001](http://127.0.0.1:5001). Vào **Danh mục** để tạo danh mục đầu tiên, sau đó vào **Sách** để thêm sách. Nhấn Ctrl+C trong PowerShell để dừng ứng dụng. Chạy lại `init_db.py` sẽ giữ dữ liệu hiện có.

## Mở dữ liệu trong DBeaver

Tạo kết nối mới và chọn **SQLite**. Trong mục **Path**, chọn tệp `data/books.db` của dự án. Cài trình điều khiển SQLite nếu DBeaver yêu cầu. Kết nối rồi mở **Tables** để xem hai bảng `categories` và `books`.

## Cấu trúc dự án

| Tệp hoặc thư mục | Vai trò |
| --- | --- |
| `app.py` | Các route, truy vấn CRUD, kiểm tra dữ liệu và thống kê Dashboard |
| `database.py` | Mở kết nối SQLite và bật kiểm tra khóa ngoại |
| `init_db.py` | Tạo thư mục dữ liệu và hai bảng nếu chúng chưa có |
| `data/books.db` | Tệp cơ sở dữ liệu SQLite |
| `templates/base.html` | Khung HTML dùng chung và menu |
| `templates/dashboard.html` | Hai tổng và bảng thống kê |
| `templates/categories.html`, `templates/books.html` | Bảng danh sách và các thao tác |
| `templates/category_form.html`, `templates/book_form.html` | Biểu mẫu dùng chung khi thêm và sửa |
| `static/style.css` | CSS thuần cho toàn bộ giao diện |
| `requirements.txt` | Phụ thuộc Flask |
| `.gitignore` | Bỏ qua môi trường ảo, bộ nhớ đệm và tệp tạm |

## Giải thích khi trình bày

- **Flask** là thư viện Python nhận yêu cầu từ trình duyệt và trả về trang web. **Route** liên kết một đường dẫn và phương thức HTTP với một hàm Python. Ví dụ, `GET /books` gọi hàm đọc danh sách sách.
- **GET** dùng để xem trang và lọc sách. **POST** gửi dữ liệu biểu mẫu để thêm, sửa hoặc xóa. Sau khi lưu thành công, Flask chuyển về trang danh sách.
- **SQLite** lưu dữ liệu trong một tệp. **CRUD** gồm tạo, đọc, cập nhật và xóa. Các lệnh SQL tương ứng là `INSERT`, `SELECT`, `UPDATE` và `DELETE`.
- **Khóa chính** `id` xác định một dòng. **Khóa ngoại** `books.category_id` trỏ đến `categories.id`. SQLite từ chối xóa danh mục khi còn sách tham chiếu đến nó.
- **Jinja** nhận dữ liệu qua `render_template()`. Biểu thức `{{ book.title }}` in tên sách, còn vòng lặp Jinja tạo một dòng bảng cho mỗi sách. Khi gửi form, Flask đọc các trường qua `request.form`.
- Dashboard dùng `COUNT(*)` để tính hai tổng. Truy vấn `LEFT JOIN` kết hợp với `GROUP BY` và `COUNT(books.id)` cho kết quả 0 với danh mục chưa có sách.
