---
description: Khởi chạy rmn-architect-reviewer để rà soát mã nguồn dự án rmn_app theo đúng chuẩn Clean Architecture và BLoC rules nội bộ.
---

# RMN App Code Review

Slash command này sẽ kích hoạt tác nhân **rmn-architect-reviewer** để quét tất cả các file Dart đang thay đổi hoặc được chỉ định.

## What This Command Does

1. **Thu thập thay đổi**: Chạy `git diff` kết hợp với `git status` để lấy danh sách các file đang được sửa đổi.
2. **Đối chiếu Rules nội bộ**: Tự động lookup 18 file rules trong `.agents/rules/` của dự án `rmn_app`.
3. **Phân tích Kiến trúc**: Rà soát nghiêm ngặt xem Component UI có bị lẫn Bussiness Logic không. Data layer có bị public ra ngoài không.
4. **Báo cáo lỗi (Report Findings)**: Trả về bảng thông báo lỗi và yêu cầu chỉnh sửa bắt buộc nếu gặp lỗi HIGH hoặc CRITICAL.

## Prerequisites

1. Đảm bảo chạy `dart analyze` (hoặc `flutter analyze`) không có lỗi cú pháp.
2. Code có thể build được thành công (`flutter pub get` và `build_runner` đã chạy nếu có generate code mới).
3. Đang đứng ở thư mục gốc của repo frontend `rmn_app/`.

## When to Use

- Trước khi tiến hành commit mã nguồn lên nhánh chính.
- Khi refactor các file lớn để đảm bảo không gãy cấu trúc `clean-architecture.md`.
- Sử dụng liên tục trong quá trình Test-Driven Development (TDD) nếu cần agent rà soát cấu trúc nhanh.

## Lệnh gọi

```
/flutter-review
```

Agent sẽ dùng quyền của mình đọc các file thay đổi hiện tại và xuất kết quả.

## Approval Criteria

- **Approve**: Kịch bản không báo bất kỳ lỗi CRITICAL hay HIGH nào.
- **Block**: Nếu gặp lỗi CRITICAL (Ví dụ: Model biết về UI widget, lơ là `mounted` async call, vi phạm rule file `.agents/rules/...`), bạn phải sửa dứt điểm trước khi merge.
