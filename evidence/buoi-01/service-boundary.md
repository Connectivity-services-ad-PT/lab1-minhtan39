# Service Boundary của nhóm

## 1. Thông tin nhóm

- Tên nhóm:1B
- Lớp:CNTT17-10
- Thành viên:Lê Minh tân, Nguyễn Hoàng Thái, Nguyễn Văn Thắng
- Service nhóm phụ trách:Camera stream
- Sản phẩm tổng thể của lớp:

## 2. Actor

Ai tương tác với hệ thống/service?

- Users/Clients (những người xem camera)
- Camera devices (các thiết bị camera phần cứng)
- Admin (quản trị viên hệ thống)
- Mobile/Web applications (các ứng dụng gọi API)

## 3. System Boundary

Nhóm em xây phần nào?

Phần nhóm kiểm soát:

- API service nhận và xử lý yêu cầu stream camera
- Database lưu trữ thông tin camera và cấu hình
- Authentication/Authorization service
- Streaming gateway (RTMP/HLS streaming protocol)
- Logging và monitoring service

Phần nhóm chỉ tích hợp:

- Hardware camera devices (chỉ tích hợp, không quản lý)
- Message queue (nếu có)
- Notification service (nếu cần thông báo)

## 4. Service Boundary

Service của nhóm có trách nhiệm gì?

- Nhận yêu cầu stream từ clients
- Xác thực và phân quyền truy cập camera
- Kết nối và lấy dữ liệu từ camera devices
- Xử lý và encode video stream
- Cung cấp stream qua các protocol (RTMP, HLS, DASH)
- Quản lý và cấu hình camera
- Ghi lại thông tin truy cập và lỗi

Service KHÔNG làm gì?

- Không quản lý hardware camera trực tiếp
- Không xử lý video analytics hoặc AI detection
- Không lưu trữ video file dài hạn (chỉ stream live)
- Không xử lý yêu cầu từ các service không được xác thực

## 5. Input / Output

### Input

- Stream request từ client (camera ID, authentication token)
- Camera configuration data (URL, credentials, resolution)
- Control commands (start/stop streaming, adjust quality)
- User authentication credentials

### Output

- Live video stream (H.264/H.265 encoded)
- Stream metadata (bitrate, resolution, FPS)
- Camera status information
- Error/failure responses
- Authentication tokens

## 6. API dự kiến

| Method | Endpoint | Mục đích |
|---|---|---|
| GET | /health | Kiểm tra service hoạt động |
| GET | /cameras | Lấy danh sách camera |
| GET | /cameras/{id} | Lấy thông tin chi tiết camera |
| POST | /cameras | Thêm camera mới |
| PUT | /cameras/{id} | Cập nhật thông tin camera |
| DELETE | /cameras/{id} | Xóa camera |
| GET | /stream/{cameraId} | Lấy video stream (WebRTC/RTMP) |
| POST | /stream/{cameraId}/start | Bắt đầu stream |
| POST | /stream/{cameraId}/stop | Dừng stream |
| GET | /cameras/{id}/status | Kiểm tra trạng thái camera |
| POST | /auth/login | Xác thực người dùng |
| POST | /auth/logout | Đăng xuất |

## 7. Phụ thuộc service khác

Service này gọi đến service nào?

- Authentication Service (xác thực người dùng)
- User/Permission Service (kiểm tra quyền truy cập)
- Notification Service (thông báo sự kiện)
- Logging Service (ghi log hoạt động)

Service nào gọi đến service này?

- Web Application (gọi để lấy stream)
- Mobile Application (gọi để lấy stream)
- Monitoring/Dashboard Service (kiểm tra trạng thái)
- Alert Service (lấy dữ liệu camera để phát hiện sự bất thường)

## 8. Sơ đồ minh họa

Có thể vẽ bằng Mermaid, draw.io, Ludichart hoặc ảnh chụp sơ đồ.
https://lucid.app/lucidchart/91e13ba9-91da-4c17-a93a-1ceeb7b76d40/edit?viewport_loc=-2227%2C-707%2C3488%2C1911%2C0_0&invitationId=inv_5f6f8537-e8d1-49b4-9e63-27ef05970c36
```mermaid
flowchart LR
    User[Actor] --> Service[Service của nhóm]
    Service --> DB[(Database)]
    Service --> Other[Service khác]
