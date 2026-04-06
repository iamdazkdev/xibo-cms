# RMN Flutter App - Global Rules (QUỐC PHÁP DỰ ÁN)

> **MỤC ĐÍCH**: Đây là bộ quy tắc cốt lõi của dự án RMN_APP. MỌI ĐẠI DIỆN AI (Architect, Engineer, UI, QA) bắt buộc phải tuyệt đối tuân thủ các quy định này, không được ngoại lệ dưới bất kỳ hình thức nào.

## 1. Clean Architecture & BLoC (Bắt buộc 100%)
- **Tuyệt đối không** gọi API trực tiếp trong UI hoặc BLoC. Mọi lệnh gọi API phải thông qua cấu trúc Data (Repository) -> Domain (UseCase) -> Presentation (BLoC).
- **Trạng thái (State)**: Luôn sử dụng Freezed để tạo BLoC State & Event. KHÔNG dùng Cubit trừ trường hợp quá đơn giản (VD: Toggle Theme).
- Mọi model phải tách biệt: `Entity` (Domain) và `Model` (Data - DTOs).

## 2. Global Error Handling & `Result<T>`
- Tuyệt đối KHÔNG quăng Exception (`throw Exception()`) ra khỏi lớp Data.
- Repository BẮT BUỢC phải trả về theo Pattern: `Future<Result<T>>` hoặc `Result<T>`.
- UI xử lý lỗi qua biến `AppError` hoặc enum mã lỗi cụ thể ở BLoC state, không nhận trực tiếp chuỗi String.

## 3. UI/UX & Design System Chung
- **Màu sắc**: Tuyệt đối KHÔNG DÙNG mã hex tĩnh (VD: `Color(0xFF...)`). MỌI màu sắc phải lấy từ Design System qua `context.colorScheme` (chuẩn AppColorScheme).
- **Văn bản**: Lấy font chữ/style từ `context.textTheme` thông qua extension `theme.dart`. Không hard-code `TextStyle`.
- **Thành phần**: Luôn ưu tiên sử dụng lại (Shared Widgets) ở `lib/core/ui/widgets/` thay vì tự build widget cục bộ mới (Ví dụ: dùng `AppGradientButton` thay vì `ElevatedButton`).

## 4. Multi-Tenant Routing
- App RMN có ba cổng riêng: Admin, Supplier, Advertiser.
- BẮT BUỢC khai báo định tuyến (Route) trong đúng file Router của hệ sinh thái đó (VD: `supplier_router.dart`, `admin_router.dart`).
- Không nối trực tiếp router chung chạ trừ khi đó là luồng Xác thực (Auth).

## 5. Cấm Vượt Quyền
- **Architect**: Chỉ quy hoạch, setup core và sơ đồ thư mục, KHÔNG viết code thân hàm logic.
- **Engineer**: Viết logic & test (TDD), kiên quyết giữ lại Clean Architecture, KHÔNG tự chế biến UI lệch chuẩn.
- **UI Designer**: Chuyên trách cắt HTML/CSS sang Widget, tập trung Responsive, KHÔNG nhúng logic BLoC vào build().
- **QA/Reviewer**: Tối ưu hoá bộ nhớ, chạy bộ build, rà soát syntax & security rò rỉ.
