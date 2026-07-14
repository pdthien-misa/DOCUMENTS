# Base Home Screen - Giải pháp Tổng quan

## Tầm nhìn
Xây dựng một trang chủ linh hoạt, cho phép mỗi người dùng tự cá nhân hóa giao diện bằng cách thêm, bớt, sắp xếp các widget theo nhu cầu công việc riêng.

## Vấn đề giải quyết
- Mỗi nhân viên có nhu cầu hiển thị thông tin khác nhau trên trang chủ
- Thông tin phân tán nhiều nơi: báo cáo, bảng tin, chỉ số KPI
- Không có cách nào để người dùng tự chọn hiển thị gì trên trang chủ
- Giao diện cố định không phù hợp với từng vai trò công việc

## Giải pháp

### Khái niệm Widget
Widget là một khối thông tin nhỏ, hiển thị một loại dữ liệu cụ thể trên trang chủ. Mỗi widget đại diện cho một功能 (chức năng) từ hệ thống.

Ví dụ:
- Widget "Báo cáo Doanh thu" hiển thị biểu đồ doanh thu
- Widget "Tin mới nhất" hiển thị danh sách tin tức gần đây
- Widget "KPI" hiển thị các chỉ số hiệu suất chính

### Loại Widget

| Nhóm | Mô tả | Ví dụ |
|------|-------|-------|
| **Báo cáo** | Hiển thị dữ liệu kinh doanh, số liệu thống kê | Doanh thu, tồn kho, tài chính |
| **Bảng tin** | Hiển thị thông tin, tin tức, thông báo | Tin mới, thông báo, danh sách công việc |
| **Phân tích** | Hiển thị biểu đồ, KPI, dashboard tổng quan | KPI widget, biểu đồ tổng hợp |

### Trang chủ Grid
Trang chủ sử dụng bố cục dạng lưới (grid) với:
- **12 cột** - cho phép widget chiếm từ 1 đến 12 cột
- **Chiều cao linh hoạt** - widget có thể cao từ 2 hàng trở lên
- **Khoảng cách** giữa các widget tự động

Ví dụ bố cục:
```
┌─────────────────────────────────┐
│         KPI Widget (12x3)       │
├────────────────┬────────────────┤
│  Báo cáo thu   │   Tồn kho      │
│    (6x4)       │    (6x4)       │
├────────────────┼────────────────┤
│  Tin mới nhất  │   Thông báo    │
│    (6x4)       │    (6x4)       │
└────────────────┴────────────────┘
```

### Chế độ Chỉnh sửa
Trang chủ có 2 chế độ:
1. **Chế độ xem**: Hiển thị widget正常 (bình thường), không cho thay đổi
2. **Chế độ chỉnh sửa**: Cho phép thêm, xóa, sắp xếp lại widget

### Tác vụ trong chế độ chỉnh sửa

| Tác vụ | Mô tả |
|--------|-------|
| **Thêm widget** | Mở danh sách widget theo nhóm, chọn widget để thêm vào trang |
| **Xóa widget** | Nhấn nút xóa trên widget để loại khỏi trang |
| **Sắp xếp lại** | Kéo thả widget đến vị trí mới trên grid |
| **Cấu hình** | Một số widget cho phép cấu hình (chọn loại báo cáo, bộ lọc...) |
| **Lưu** | Lưu layout hiện tại, lần sau mở trang sẽ hiển thị lại đúng vị trí |
| **Hủy** | Quay về layout trước khi chỉnh sửa |

### Hộp thoại Chọn Widget
Khi nhấn "Thêm thành phần", hiển thị hộp thoại với:
- Widget được nhóm theo thể loại (Báo cáo, Bảng tin, Phân tích)
- Mỗi widget hiển thị: icon, tên, mô tả ngắn
- Nhấn "+" để thêm widget vào trang

### Widget có thể cấu hình
Một số widget cho phép người dùng cấu hình:
- **Báo cáo Doanh thu**: Chọn khoảng thời gian, loại biểu đồ
- **Tin mới nhất**: Chọn số lượng tin hiển thị
- **KPI**: Chọn KPI cần theo dõi

### Lưu giữ Layout
- Mỗi người dùng có layout riêng
- Layout bao gồm: danh sách widget + vị trí (x, y) + kích thước (w, h) + config
- Khi lưu: xóa layout cũ, ghi layout mới (đảm bảo consistency)
- Khi mở trang: load layout đã lưu, nếu chưa có thì hiển thị layout mặc định

### Layout Mặc định
Khi người dùng chưa lưu layout, hiển thị mặc định:
- Báo cáo Doanh thu (bên trái)
- Tồn kho (bên phải)
- Tin mới nhất (bên trái)

## Luồng Sử dụng

### Luồng 1: Xem trang chủ
```
Mở trang → Load layout đã lưu → Hiển thị widget theo vị trí đã lưu
```

### Luồng 2: Thêm widget
```
Nhấn "Chỉnh sửa" → Nhấn "Thêm thành phần" → Chọn widget từ hộp thoại
→ Widget xuất hiện trên grid → Nhấn "Lưu"
```

### Luồng 3: Sắp xếp lại widget
```
Nhấn "Chỉnh sửa" → Kéo widget đến vị trí mới → Nhấn "Lưu"
```

### Luồng 4: Xóa widget
```
Nhấn "Chỉnh sửa" → Nhấn "✕" trên widget → Widget biến mất → Nhấn "Lưu"
```

## Quy tắc Kinh doanh

1. **Mỗi user có tối đa 1 instance mỗi loại widget** (không trùng widget_key trong cùng layout)
2. **Widget không active sẽ không hiển thị** trong hộp thoại chọn
3. **Layout lưu theo user_id** - mỗi người dùng độc lập
4. **Config JSON của widget được lưu cùng layout** - giữ nguyên cấu hình khi reload
5. **Khi widget thay đổi kích thước default ở admin → user giữ nguyên size đã lưu**

## Trạng thái Hệ thống

| Trạng thái | Mô tả |
|------------|-------|
| **Đang tải** | Hiển thị spinner khi load layout |
| **Bình thường** | Hiển thị widget |
| **Đang chỉnh sửa** | Hiển thị toolbar, nút thêm/xóa, cho phép kéo thả |
| **Có thay đổi** | Hiển thị nút "Lưu" (đã có thay đổi chưa lưu) |
