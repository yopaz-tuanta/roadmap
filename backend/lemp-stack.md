# LEMP Stack

## LEMP Stack là gì?

**LEMP Stack** là một bộ công nghệ phổ biến được thiết kế để vận hành và quản lý các ứng dụng web.

LEMP gồm:

| Chữ cái | Công nghệ | Vai trò |
| --- | --- | --- |
| L | Linux | Hệ điều hành chạy trên server. |
| E | Nginx | Web server nhận request từ client. |
| M | MySQL | Hệ quản trị cơ sở dữ liệu. |
| P | PHP | Ngôn ngữ xử lý logic phía server. |

## Tại sao cần tới Nginx?

**Nginx** là một web server. Nó nhận request từ client, xử lý trực tiếp nếu có thể, hoặc chuyển request vào backend/server phù hợp để xử lý.

Nginx thường được dùng vì:

- Xử lý file tĩnh rất nhanh, ví dụ ảnh, CSS, JavaScript.
- Nếu tìm thấy file tĩnh, Nginx trả file trực tiếp mà không cần chuyển vào application server.
- Có thể cache ảnh, CSS và các tài nguyên tĩnh để tăng tốc website.
- Chuyển các request PHP sang **PHP-FPM** để thực thi code PHP/Laravel.

**PHP-FPM** là thành phần quản lý các tiến trình PHP và thực thi code PHP khi Nginx chuyển request tới.

## Nginx khác gì Apache?

Nginx và Apache đều là web server, nhưng khác nhau ở kiến trúc xử lý request và cách cấu hình.

### Kiến trúc

| Tiêu chí | Nginx | Apache |
| --- | --- | --- |
| Mô hình xử lý | Event-driven, bất đồng bộ. Một worker có thể quản lý hàng nghìn kết nối cùng lúc. | Mỗi request thường được gán cho một process hoặc thread để xử lý. |
| Khả năng chịu tải | Tốt khi có nhiều kết nối đồng thời. | RAM tăng nhanh khi số lượng request tăng cao. |
| Xử lý PHP | Không tự xử lý PHP trực tiếp, thường proxy request sang PHP-FPM. | Có thể tích hợp module như `mod_php` để xử lý ngay bên trong server. |
| File tĩnh | Xử lý file tĩnh rất nhanh, trả kết quả trực tiếp nếu tìm thấy. | Khi dùng module tích hợp, xử lý file tĩnh vẫn có thể phải đi qua nhiều lớp xử lý hơn. |

Ví dụ với Apache theo mô hình process/thread:

```text
1000 request => có thể cần 1000 process/thread
```

Điều này khiến RAM tăng nhanh khi lượng request lớn.

### Cấu hình

| Tiêu chí | Nginx | Apache |
| --- | --- | --- |
| File cấu hình | Không hỗ trợ `.htaccess`. Cấu hình thường tập trung ở `/etc/nginx/sites-available/`. | Hỗ trợ `.htaccess` ở từng thư mục. |
| Reload cấu hình | Cần reload/restart Nginx sau khi đổi cấu hình. | Có thể cấu hình linh hoạt hơn ở cấp thư mục. |
| HTTP/2 | Hỗ trợ HTTP/2 dễ dàng. | Có hỗ trợ HTTP/2 nhưng việc tùy chỉnh thường không thuận tiện bằng Nginx. |

## HTTP/1.1 khác gì HTTP/2?

**HTTP/2** là phiên bản nâng cấp lớn của HTTP/1.1, được thiết kế để tăng tốc độ truyền tải trang web trên trình duyệt.

| Tiêu chí | HTTP/1.1 | HTTP/2 |
| --- | --- | --- |
| Kết nối | Trình duyệt chỉ có thể gửi một request tại một thời điểm trên một kết nối TCP. | Cho phép gửi nhiều request và nhận nhiều response cùng lúc trên một kết nối TCP duy nhất. |
| Cơ chế chính | Dễ bị chậm khi có nhiều tài nguyên cần tải. | Multiplexing, tức đa luồng trên cùng một kết nối. |
| Dạng dữ liệu | Text. | Binary. |
| Header | Mỗi request gửi lại toàn bộ header. | Dùng HPACK Compression để nén header. |

Với HTTP/2, header chỉ cần gửi đầy đủ lần đầu. Các lần sau chỉ gửi phần thay đổi, giúp tiết kiệm băng thông.

## VPS là gì?

**VPS** (Virtual Private Server) là máy chủ ảo cá nhân.

VPS được tạo ra bằng cách chia một máy chủ vật lý thành nhiều máy chủ ảo khác nhau thông qua công nghệ ảo hóa. Mỗi VPS có hệ điều hành riêng, CPU, RAM, dung lượng ổ cứng riêng và hoạt động biệt lập với các VPS khác.

## VPS khác gì server vật lý?

| Tiêu chí | VPS | Server vật lý / Dedicated Server |
| --- | --- | --- |
| Bản chất | Là một phần tài nguyên được chia nhỏ từ máy vật lý bằng phần mềm ảo hóa. | Là toàn bộ phần cứng của một máy tính vật lý thật. |
| Hiệu năng | Phụ thuộc vào tổng tài nguyên của máy chủ cha. | Có thể dùng 100% hiệu năng phần cứng, không chia sẻ với ai. |
| Độ ổn định | Tốt, nhưng vẫn phụ thuộc vào tài nguyên và cấu hình của máy chủ cha. | Rất ổn định vì toàn bộ tài nguyên thuộc về một server duy nhất. |
| Khả năng mở rộng | Dễ dàng và nhanh chóng, có thể nâng cấp thêm RAM, CPU, SSD. | Khó và tốn thời gian hơn vì phải mua, lắp đặt hoặc thay phần cứng vật lý. |
| Chi phí | Rẻ, hợp lý | Rất đắt, gồm chi phí mua máy, thuê chỗ đặt và bảo trì phần cứng. |
