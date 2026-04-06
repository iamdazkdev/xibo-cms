# Kế hoạch phát triển CMS Admin App (Flutter)

Mục tiêu của kế hoạch này là xây dựng một ứng dụng Flutter (ưu tiên Web cho bản MVP, nhưng vẫn duy trì cấu trúc thích hợp để mở rộng ra Mobile) đóng vai trò làm CMS Admin Panel, tương tác với Xibo CMS thông qua hệ thống REST API. Toàn bộ UI/UX sẽ kế thừa và tái sử dụng lại từ dự án `rmn_app`.

## Cấu trúc và Vị trí dự án

Dự án sẽ được khởi tạo ngay bên trong thư mục hệ thống hiện tại của Xibo CMS:
- **Tên dự án:** `rmn_cms_app`
- **Đường dẫn:** `/Users/iamdazkdev/WebstormProjects/kway-vina/xibo-cms/rmn_cms_app`

## Kiến trúc và Công nghệ (Tech Stack)

Sử dụng lại chuẩn Clean Architecture và các thành phần đã thành công từ `rmn_app`:
1. **Kiến trúc (Architecture):** Clean Architecture kết hợp Feature-first. Đảm bảo logic UI và Business hoàn toàn tách biệt (đây là điều kiện tiên quyết để app Web dễ dàng build sang Mobile sau này).
2. **Quản lý trạng thái:** `BLoC` / `Cubit`.
3. **Mạng lưới (Network):** `Dio` - kèm các Interceptor quản lý Header OAuth2 (cho Xibo API) và xử lý tự động làm mới Token.
4. **Định tuyến:** `go_router` - Cực kỳ quan trọng để xử lý URL Routing cho nền tảng Web.
5. **UI/UX:** Nhập (Import) bộ Framework UI của `rmn_app`, bao gồm Layout Responsive Sidebar, Design System (Màu sắc, Typography) và hệ thống biểu mẫu Form Pattern.

## Lộ trình phát triển (Phases)

### Giai đoạn 1: Khởi tạo Core Project
- Khởi tạo project `rmn_cms_app` bằng chuẩn Flutter template.
- Cấu hình file `pubspec.yaml` với tất cả các package thiết yếu.
- Phân rã cấu trúc thư mục lib (`core/`, `features/`).

### Giai đoạn 2: Tích hợp thư viện UI & App Shell
- Port mã nguồn bộ Theme, Components cơ bản từ `rmn_app` sang.
- Thiết lập Master Layout (Sidebar trái, Header điều hướng trang trên Web).

### Giai đoạn 3: Module Mạng lưới (Network) & Xác thực
- Xây dựng lớp `XiboApiClient` giao tiếp với host port mặc định của Xibo (localhost / dev server).
- Làm màn hình Login/Auth, lấy Token lưu vào thiết bị (Local Storage cho Web).

### Giai đoạn 4: Dashboard & Tính năng
- Đọc tài liệu Swagger để thiết lập các mô hình dữ liệu.
- Làm các luồng fetch Data và hiển thị bảng dữ liệu (DataTables).

## Kế hoạch nghiệm thu (Verification Plan)
- Project build và chạy thử được bằng lệnh `flutter run -d chrome`.
- Đảm bảo hot reload và cấu trúc module Clean Architecture tuân thủ chuẩn của team.
