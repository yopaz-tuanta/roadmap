# Các câu lệnh git thường dùng

## git clone

Sao chép một repository từ remote về máy local.

## git pull

Lấy code mới nhất từ remote về và gộp vào branch hiện tại.

## git fetch

Lấy thông tin mới nhất từ remote về nhưng chưa gộp vào code hiện tại.

## git checkout

Chuyển sang một branch hoặc một commit khác.

## git checkout -b

Tạo branch mới và chuyển sang branch đó ngay lập tức.

## git branch

Hiển thị danh sách branch trong repository.

## git status

Kiểm tra trạng thái hiện tại của repository, ví dụ file nào đã thay đổi, file nào đã được add.

## git add .

Đưa tất cả file đã thay đổi vào vùng chờ commit.

## git commit -m

Tạo một commit mới kèm theo nội dung mô tả.

## git push

Đẩy commit từ máy local lên remote repository.

## git cherry-pick

Lấy một commit cụ thể từ branch khác và áp dụng vào branch hiện tại.

## git stash

Lưu tạm các thay đổi chưa commit để có thể chuyển branch hoặc làm việc khác.

## git log

Xem lịch sử commit của repository.

## git status

Kiểm tra lại trạng thái hiện tại của repository.

# Phần 2: Git flow trong dự án thường dùng

## Các nhánh chính

- Nhánh production: `main`
- Nhánh test của khách hàng: `staging`
- Nhánh phát triển: `develop`

## Khi phát triển tính năng mới

- Checkout nhánh mới từ `develop` với tên dạng `feat/new-feature-name`.
- Thay đổi version, README nếu cần.
- Tạo PR vào `develop`.

## Khi fix bug

- Checkout nhánh mới từ `develop` với tên dạng `fix/bug-name`.
- Thay đổi version, README nếu cần.
- Tạo PR vào `develop`.

## Khi cần đẩy lên staging

- Tạo PR từ `develop` vào `staging`.

## Khi cần đẩy lên production

- Tạo PR từ `staging` vào `main`.

## Lưu ý khi cần hot fix trên môi trường staging hoặc production

- Checkout nhánh hot fix từ đúng môi trường cần sửa, ví dụ từ `staging` hoặc `main`.
- Tạo PR merge lại vào đúng môi trường đó.
- Sau khi hot fix được merge, cần merge lại thay đổi vào `develop` để tránh mất code sửa lỗi ở các lần release sau.
