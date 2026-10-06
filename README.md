# Bài Lab Day 20 - Product Metrics

**Họ tên:** Đinh Xuân Quyền
**Mã học viên (MHV):** 2A202602358
**Dự án chọn làm:** SmartCheck-in Kiosk

- [Link tệp Metrics Pack](./metrics-pack.md)

## Điều tôi mang về áp dụng cho dự án thật

Qua bài lab này, em rút ra được những bài học quan trọng để áp dụng vào dự án thực tế:

1. **Xác định Core Action đúng bản chất**: Core Action phải gắn liền với khoảnh khắc người dùng nhận được giá trị thực sự (như hoàn tất Live Face để được check-in nhanh), chứ không phải các thao tác bề nổi (như bấm nút "Bắt đầu").
2. **Thiết lập Metric System toàn diện và cân bằng**: Không chỉ theo dõi North Star Metric để đo lường thành công, mà phải luôn đi kèm Counter-metric (Tỉ lệ chuyển giao lễ tân, thời gian hoàn thành) để kiểm soát rủi ro, tránh trải nghiệm người dùng bị giảm sút do lỗi hệ thống.
3. **Định nghĩa Cadence & Retention tự nhiên**: Nhịp độ đo lường phải dựa trên chu kỳ sử dụng thực tế của khách hàng (theo từng chuyến đi/booking) thay vì áp dụng máy móc theo Daily/Weekly/Monthly.
4. **Kỷ luật trong Tracking**: Việc định nghĩa Acceptance Criteria chặt chẽ cho event (chống ghi trùng, đo lường hành vi thực tế thay vì ý định) là  quan trọng để đảm bảo dữ liệu phân tích luôn chính xác.
