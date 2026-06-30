# SQL

## Checklist

- [-] SQL Basic
- [-] Join
- [-] Aggregate
- [-] Database Design
- [-] Migrations
- [-] Constraint
- [-] Index
- [-] N+1 Problem

## SQL Basic

### SQL Basic là gì?

**SQL** (Structured Query Language) là ngôn ngữ dùng để làm việc với cơ sở dữ liệu quan hệ, ví dụ MySQL, PostgreSQL, SQL Server.

SQL cho phép ta thao tác với dữ liệu bằng các câu lệnh có cấu trúc rõ ràng thay vì tự đọc ghi file thủ công.

Các nhóm câu lệnh SQL thường gặp:

| Nhóm | Mục đích | Ví dụ |
| --- | --- | --- |
| DDL | Định nghĩa cấu trúc database | `CREATE`, `ALTER`, `DROP` |
| DML | Thao tác dữ liệu | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| DCL | Phân quyền | `GRANT`, `REVOKE` |
| TCL | Quản lý transaction | `BEGIN`, `COMMIT`, `ROLLBACK` |

### SQL Basic dùng để làm gì?

SQL Basic dùng để đọc, thêm, sửa, xóa dữ liệu trong database.

Ví dụ:

```sql
SELECT id, name, email
FROM users
WHERE status = 'active'
ORDER BY created_at DESC
LIMIT 10;
```

Ví dụ với **DCL**:

```sql
GRANT SELECT, INSERT
ON shop.orders
TO 'report_user'@'localhost';
```

Câu lệnh trên cấp quyền đọc và thêm dữ liệu vào bảng `orders` trong database `shop` cho user `report_user`.

```sql
REVOKE INSERT
ON shop.orders
FROM 'report_user'@'localhost';
```

Câu lệnh trên thu hồi quyền thêm dữ liệu vào bảng `orders` của user `report_user`.

Ví dụ với **TCL**:

```sql
BEGIN;

UPDATE accounts
SET balance = balance - 100000
WHERE id = 1;

UPDATE accounts
SET balance = balance + 100000
WHERE id = 2;

COMMIT;
```

Transaction trên chuyển 100000 từ tài khoản `id = 1` sang tài khoản `id = 2`. Nếu cả hai câu `UPDATE` đều thành công thì `COMMIT` để lưu thay đổi.

## Join

### Join là gì?

**Join** là cách kết hợp dữ liệu từ nhiều bảng dựa trên mối quan hệ giữa các cột.

Ví dụ hệ thống có bảng `users` và `orders`:

- `users.id`: id của user
- `orders.user_id`: user đã tạo order

Muốn lấy thông tin order kèm tên user, ta cần join hai bảng.

```sql
SELECT orders.id, orders.total, users.name
FROM orders
JOIN users ON users.id = orders.user_id;
```

### Join dùng để làm gì?

Join dùng để truy vấn dữ liệu có quan hệ với nhau mà không cần lưu trùng dữ liệu ở nhiều nơi.

Các loại join phổ biến:

| Loại join | Ý nghĩa |
| --- | --- |
| `INNER JOIN` | Chỉ lấy dữ liệu khớp ở cả hai bảng. |
| `LEFT JOIN` | Lấy toàn bộ dữ liệu bảng bên trái, dù bảng bên phải không có dữ liệu khớp. |
| `RIGHT JOIN` | Lấy toàn bộ dữ liệu bảng bên phải, dù bảng bên trái không có dữ liệu khớp. |
| `FULL OUTER JOIN` | Lấy dữ liệu từ cả hai bảng, kể cả khi không khớp. PostgreSQL hỗ trợ, MySQL không hỗ trợ trực tiếp. |
| `CROSS JOIN` | Ghép mọi dòng của bảng A với mọi dòng của bảng B. |


## Aggregate

### Aggregate là gì?

**Aggregate** là nhóm hàm dùng để tính toán trên nhiều dòng dữ liệu và trả về một kết quả tổng hợp.

Các hàm aggregate phổ biến:

| Hàm | Ý nghĩa |
| --- | --- |
| `COUNT()` | Đếm số dòng. |
| `SUM()` | Tính tổng. |
| `AVG()` | Tính trung bình. |
| `MIN()` | Lấy giá trị nhỏ nhất. |
| `MAX()` | Lấy giá trị lớn nhất. |

### Aggregate dùng để làm gì?

Aggregate dùng để làm báo cáo, thống kê và phân tích dữ liệu.

Ví dụ tính tổng doanh thu theo từng user:

```sql
SELECT user_id, SUM(total) AS total_revenue
FROM orders
GROUP BY user_id;
```

Ví dụ lọc nhóm bằng `HAVING`:

```sql
SELECT user_id, COUNT(*) AS order_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) >= 5;
```

`WHERE` lọc dữ liệu trước khi group. `HAVING` lọc kết quả sau khi group.

## Database Design

### Database Design là gì?

**Database Design** là quá trình thiết kế cấu trúc database: cần những bảng nào, mỗi bảng có những cột nào, quan hệ giữa các bảng ra sao và cần ràng buộc dữ liệu như thế nào.

Một database tốt cần:

- Lưu đúng dữ liệu nghiệp vụ.
- Tránh trùng lặp dữ liệu không cần thiết.
- Dễ truy vấn.
- Dễ mở rộng.
- Giữ được tính toàn vẹn dữ liệu.

### Database Design dùng để làm gì?

Database Design giúp hệ thống lưu dữ liệu rõ ràng, ít lỗi và dễ bảo trì.

Các loại quan hệ thường gặp:

| Quan hệ | Ví dụ |
| --- | --- |
| One-to-one | Một user có một profile. |
| One-to-many | Một user có nhiều orders. |
| Many-to-many | Một product thuộc nhiều categories, một category có nhiều products. |

### Normalization là gì?

**Normalization** là kỹ thuật tách dữ liệu thành nhiều bảng hợp lý để giảm trùng lặp và tránh sai lệch dữ liệu.

Ví dụ không nên lưu tên user trực tiếp trong bảng `orders` nếu đã có `user_id`. Nếu user đổi tên, chỉ cần sửa ở bảng `users`.

Tuy nhiên trong một số hệ thống cần hiệu năng đọc cao, có thể dùng **denormalization**: cố ý lưu trùng một phần dữ liệu để query nhanh hơn. Việc này cần cân nhắc vì dữ liệu trùng có thể bị lệch nếu update không đồng bộ.

## Migrations

### Migrations là gì?

**Migration** là file mô tả sự thay đổi cấu trúc database theo thời gian.

Ví dụ:

- Tạo bảng mới.
- Thêm cột mới.
- Đổi kiểu dữ liệu.
- Thêm index.
- Thêm constraint.
- Xóa cột không còn dùng.

### Migrations dùng để làm gì?

Migration giúp version control cấu trúc database giống như source code.

Lợi ích:

- Mọi developer dùng cùng một cấu trúc database.
- Deploy lên staging/production có lịch sử thay đổi rõ ràng.
- Có thể rollback nếu migration được viết đúng.
- Tránh sửa database thủ công rồi quên ghi lại.

Khi viết migration cần cẩn thận với bảng lớn. Các thao tác như thêm index, đổi kiểu cột, thêm constraint có thể lock bảng hoặc chạy rất lâu nếu dữ liệu nhiều.

## Constraint

### Constraint là gì?

**Constraint** là ràng buộc dữ liệu ở cấp database.

Constraint giúp database tự kiểm tra dữ liệu trước khi cho phép insert hoặc update.

Các constraint phổ biến:

| Constraint | Ý nghĩa |
| --- | --- |
| `PRIMARY KEY` | Định danh duy nhất cho mỗi dòng. |
| `FOREIGN KEY` | Đảm bảo dữ liệu tham chiếu tồn tại ở bảng khác. |
| `UNIQUE` | Không cho phép trùng giá trị. |
| `NOT NULL` | Không cho phép giá trị rỗng. |
| `CHECK` | Kiểm tra điều kiện cụ thể. |
| `DEFAULT` | Giá trị mặc định nếu không truyền vào. |

### Constraint dùng để làm gì?

Constraint dùng để bảo vệ tính đúng đắn của dữ liệu ngay tại database.

Ví dụ:

```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    user_id BIGINT NOT NULL,
    total DECIMAL(12, 2) NOT NULL CHECK (total >= 0),
    status VARCHAR(50) NOT NULL DEFAULT 'pending',
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

Nếu application có bug và cố lưu order với `user_id` không tồn tại hoặc `total < 0`, database sẽ từ chối.

Không nên chỉ validate ở application. Validation ở application giúp báo lỗi đẹp hơn, còn constraint ở database là lớp bảo vệ cuối cùng để dữ liệu không bị sai.

## Index

### Index là gì?

**Index** là cấu trúc dữ liệu phụ được database tạo ra để tìm dữ liệu nhanh hơn.

Có thể hiểu index giống mục lục của một cuốn sách. Nếu không có mục lục, muốn tìm một chủ đề phải đọc lần lượt từ đầu tới cuối. Nếu có mục lục, ta tìm đúng mục rồi nhảy tới trang cần đọc.

Ví dụ:

```sql
CREATE INDEX idx_users_email ON users(email);
```

Khi query:

```sql
SELECT *
FROM users
WHERE email = 'an@example.com';
```

Database có thể dùng index `idx_users_email` để tìm nhanh user theo email thay vì scan toàn bộ bảng `users`.

### Index dùng để làm gì?

Index dùng để tăng tốc các query đọc dữ liệu, đặc biệt là các query có:

- `WHERE`
- `JOIN`
- `ORDER BY`
- `GROUP BY`
- `DISTINCT`
- Tìm kiếm text
- Tìm kiếm dữ liệu JSON/array/spatial tùy database

Index không thay đổi kết quả query. Nó chỉ thay đổi cách database tìm dữ liệu.

### Tại sao index làm query nhanh hơn?

Nếu không có index, database thường phải dùng đọc nhiều dòng trong bảng để tìm dòng phù hợp.

Ví dụ bảng `users` có 10 triệu dòng:

```sql
SELECT *
FROM users
WHERE email = 'an@example.com';
```

Nếu không có index trên `email`, database có thể phải kiểm tra từng dòng.

Nếu có index trên `email`, database đi qua cấu trúc cây đã được sắp xếp để tìm giá trị cần tìm. Số bước tìm kiếm giảm rất nhiều so với đọc toàn bộ bảng.

Index còn nhanh hơn vì:

- Dữ liệu trong index thường nhỏ hơn dữ liệu trong bảng.
- Index đã được sắp xếp theo key.
- Database có thể đọc ít page hơn từ disk/memory.
- Một số query có thể dùng **covering index** để lấy đủ dữ liệu từ index mà không cần quay lại bảng chính.

Ví dụ covering index:

```sql
CREATE INDEX idx_orders_user_status_total
ON orders(user_id, status, total);

SELECT user_id, status, total
FROM orders
WHERE user_id = 10 AND status = 'paid';
```

Nếu database lấy đủ `user_id`, `status`, `total` từ index, nó có thể giảm số lần đọc bảng `orders`.

### Có những loại index nào trong MySQL?

Trong MySQL, cần phân biệt hai cách gọi:

- Loại theo ràng buộc hoặc mục đích: `PRIMARY`, `UNIQUE`, `INDEX`, `FULLTEXT`, `SPATIAL`.
- Loại theo cấu trúc lưu trữ: thường gặp nhất là B-tree/B+tree, Hash, R-tree tùy storage engine.

Các loại thường gặp:

| Loại index | Dùng khi nào |
| --- | --- |
| `PRIMARY KEY` | Cột định danh chính của bảng, ví dụ `id` |
| `UNIQUE` | Cần đảm bảo không trùng dữ liệu, ví dụ `users.email`, `users.phone`. |
| Normal index | Tăng tốc tìm kiếm, join, sort trên cột thường dùng. |
| Composite index | Query thường lọc hoặc sort theo nhiều cột cùng lúc, ví dụ `(user_id, status, created_at)`. |
| Prefix index | Cột text dài nhưng chỉ cần index một phần đầu, ví dụ `VARCHAR(500)` hoặc `TEXT`. |
| FULLTEXT index | Tìm kiếm toàn văn trên `CHAR`, `VARCHAR`, `TEXT`, ví dụ tìm bài viết theo nội dung. |
| SPATIAL index | Dữ liệu không gian như point, polygon, geometry. |
| Functional index | Query lọc theo biểu thức, ví dụ index trên `LOWER(email)` hoặc giá trị trích từ JSON. |
| Hash index | Chủ yếu gặp ở Memory engine hoặc một số cơ chế nội bộ. Phù hợp so sánh bằng `=`, không phù hợp range query. |

Với MySQL InnoDB, index phổ biến nhất trong ứng dụng backend thường là B+tree index.

### Có những loại index nào trong PostgreSQL?

PostgreSQL hỗ trợ nhiều index access method hơn MySQL.

| Loại index | Dùng khi nào |
| --- | --- |
| B-tree | Mặc định. Dùng cho hầu hết query `=`, range, `ORDER BY`, `JOIN` trên số, text, timestamp. |
| Hash | Chỉ tối ưu cho so sánh bằng `=`. Ít dùng hơn B-tree vì B-tree cũng xử lý equality tốt và linh hoạt hơn. |
| GIN | Dữ liệu chứa nhiều phần tử bên trong một cột, ví dụ `jsonb`, array, full-text search. |
| GiST | Dữ liệu hình học, range, full-text search, nearest-neighbor, dữ liệu cần toán tử đặc biệt như overlap. |
| SP-GiST | Dữ liệu có thể chia không gian hoặc phân vùng tự nhiên, ví dụ point, IP, text prefix tùy operator class. |
| BRIN | Bảng rất lớn, dữ liệu có tương quan với vị trí lưu trữ vật lý, ví dụ `created_at` tăng dần theo thời gian. |
| Bloom extension | Index xác suất qua extension, có thể dùng cho một số trường hợp nhiều cột nhưng chấp nhận false positive. |

PostgreSQL còn có các biến thể quan trọng:

| Biến thể | Ý nghĩa |
| --- | --- |
| Composite index | Index nhiều cột, ví dụ `(user_id, status, created_at)`. |
| Partial index | Chỉ index một phần dữ liệu, ví dụ chỉ index order có `status = 'paid'`. |
| Expression index | Index trên biểu thức, ví dụ `LOWER(email)`. |
| Covering index | Dùng `INCLUDE` để thêm cột phục vụ index-only scan. |
| Unique index | Đảm bảo không trùng dữ liệu. |

### Khi nào sử dụng index nào?

Quy tắc thực tế:

| Nhu cầu | Index nên cân nhắc |
| --- | --- |
| Tìm theo `id` | `PRIMARY KEY`. |
| Tìm theo email, username, slug duy nhất | `UNIQUE` index. |
| Lọc bằng `=`, `>`, `<`, `BETWEEN`, `IN` | B-tree. |
| Join hai bảng qua foreign key | Index trên cột foreign key, ví dụ `orders.user_id`. |
| Query thường lọc nhiều cột cùng lúc | Composite index theo đúng thứ tự sử dụng trong query. |
| Query vừa lọc vừa sort | Composite index đặt cột filter trước, cột sort sau nếu phù hợp. |
| Tìm text kiểu search engine đơn giản | FULLTEXT trong MySQL, GIN/GiST full-text trong PostgreSQL. |
| Query `jsonb`, array trong PostgreSQL | GIN. |
| Dữ liệu địa lý, hình học, range overlap | GiST hoặc SP-GiST trong PostgreSQL, SPATIAL trong MySQL. |
| Bảng log rất lớn, dữ liệu tăng theo thời gian | BRIN trong PostgreSQL. |
| Chỉ cần index một nhóm nhỏ dữ liệu | Partial index trong PostgreSQL. |
| Query dùng function trong `WHERE` | Functional/expression index. |

Ví dụ composite index:

```sql
CREATE INDEX idx_orders_user_status_created_at
ON orders(user_id, status, created_at);
```

Index này hợp với query:

```sql
SELECT *
FROM orders
WHERE user_id = 10
  AND status = 'paid'
ORDER BY created_at DESC;
```

Thứ tự cột trong composite index rất quan trọng. Index `(user_id, status)` khác với `(status, user_id)` nếu độ chọn lọc và pattern query khác nhau.

### Khi nào không nên đánh index?

Không nên thêm index chỉ vì "có thể sau này cần".

Nên tránh hoặc cân nhắc kỹ khi:

- Bảng rất nhỏ, scan toàn bảng đã đủ nhanh.
- Cột có quá ít giá trị khác nhau, ví dụ `gender`, `is_active`, nếu query không đủ chọn lọc.
- Cột ít khi được dùng trong `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY`.
- Query trả về phần lớn dữ liệu trong bảng.
- Hệ thống ghi dữ liệu rất nhiều và index đó không thật sự phục vụ query quan trọng.
- Index bị trùng với index khác.

Ví dụ nếu bảng có 1 triệu user và 900 nghìn user có `is_active = 1`, index riêng trên `is_active` thường không giúp nhiều cho query:

```sql
SELECT *
FROM users
WHERE is_active = 1;
```

Vì kết quả trả về quá nhiều dòng, database vẫn phải đọc phần lớn bảng.

### Đánh index nhiều có ảnh hưởng gì không?

Có. Index giúp đọc nhanh hơn nhưng làm ghi chậm hơn và tốn tài nguyên hơn.

Ảnh hưởng chính:

| Ảnh hưởng | Giải thích |
| --- | --- |
| Chậm `INSERT` | Mỗi lần thêm dòng, database phải thêm dữ liệu vào các index liên quan. |
| Chậm `UPDATE` | Nếu update cột nằm trong index, database phải cập nhật index. |
| Chậm `DELETE` | Database phải xóa entry tương ứng trong index. |
| Tốn disk | Mỗi index là một cấu trúc dữ liệu riêng. |
| Tốn memory/cache | Index chiếm bộ nhớ cache, có thể đẩy dữ liệu quan trọng khác ra ngoài. |
| Tăng chi phí migration | Tạo index trên bảng lớn có thể mất thời gian và ảnh hưởng production. |
| Query planner khó chọn hơn | Quá nhiều index gần giống nhau có thể làm hệ thống khó tối ưu và khó bảo trì. |

Vì vậy cần đo bằng `EXPLAIN` hoặc `EXPLAIN ANALYZE` trước và sau khi thêm index.

Ví dụ:

```sql
EXPLAIN
SELECT *
FROM orders
WHERE user_id = 10
  AND status = 'paid';
```

Trong PostgreSQL có thể dùng:

```sql
EXPLAIN ANALYZE
SELECT *
FROM orders
WHERE user_id = 10
  AND status = 'paid';
```

### Checklist khi thêm index

Trước khi thêm index, nên tự hỏi:

- Query nào đang chậm?
- Query đó lọc, join, sort theo cột nào?
- Query trả về ít dòng hay nhiều dòng?
- Cột đó có độ chọn lọc cao không?
- Index mới có trùng với index cũ không?
- Hệ thống đọc nhiều hay ghi nhiều?
- Có đo bằng `EXPLAIN` hoặc `EXPLAIN ANALYZE` chưa?

## N+1 Problem

### N+1 Problem là gì?

**N+1 Problem** là lỗi hiệu năng xảy ra khi application query 1 lần để lấy danh sách chính, sau đó query thêm N lần để lấy dữ liệu liên quan cho từng item.

Ví dụ:

```text
1 query lấy 100 users
100 query lấy orders của từng user
= 101 query
```

Nếu số user tăng lên 1000, số query tăng thành 1001.

### N+1 Problem dùng để giải quyết vấn đề gì?

N+1 không phải là kỹ thuật để dùng, mà là vấn đề cần phát hiện và xử lý.

N+1 thường xuất hiện khi dùng ORM như Laravel Eloquent, Django ORM, Rails ActiveRecord.

Ví dụ dễ gây N+1:

```php
$users = User::all();

foreach ($users as $user) {
    echo $user->orders->count();
}
```

Nếu `orders` chưa được eager load, mỗi user có thể sinh thêm một query.

Cách xử lý:

- Dùng eager loading.
- Dùng `JOIN` nếu phù hợp.
- Dùng aggregate query.
- Dùng batch loading.
- Kiểm tra query log hoặc debug toolbar.

Ví dụ dùng eager loading trong Laravel:

```php
$users = User::with('orders')->get();
```

Ví dụ dùng aggregate:

```sql
SELECT users.id, users.name, COUNT(orders.id) AS order_count
FROM users
LEFT JOIN orders ON orders.user_id = users.id
GROUP BY users.id, users.name;
```

Cách tốt nhất là không chỉ nhìn code, mà phải nhìn số lượng query thật sự được chạy khi request được xử lý.
