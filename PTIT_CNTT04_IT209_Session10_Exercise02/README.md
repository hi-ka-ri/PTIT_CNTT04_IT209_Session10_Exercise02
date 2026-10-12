# Bài 2: Đóng gói ứng dụng Web Backend bằng Single-stage Dockerfile

## 1. Giới thiệu
Dự án đóng gói một ứng dụng Web Backend (Node.js/Express) đơn giản thành Docker Image bằng kỹ thuật **Single-stage Dockerfile** nhằm phục vụ triển khai lên môi trường chạy thử nghiệm.

---

## 2. Cấu trúc thư mục
```text
.
├── Dockerfile          # Định nghĩa cấu hình đóng gói image
├── .dockerignore       # Loại trừ các file/thư mục không cần thiết khi build
├── package.json        # Khai báo metadata và dependencies của dự án Node.js
├── server.js           # Mã nguồn Web API Backend
└── README.md           # Hướng dẫn chi tiết cách build, chạy và kiểm tra
```

---

## 3. Giải thích Dockerfile
Tệp `Dockerfile` bao gồm đầy đủ các chỉ thị tiêu chuẩn:
- **`FROM node:18-alpine`**: Sử dụng base image Node.js 18 phiên bản Alpine Linux nhẹ, tối ưu tài nguyên và dung lượng image.
- **`WORKDIR /app`**: Thiết lập thư mục làm việc mặc định bên trong container là `/app`.
- **`ENV PORT=3000`**: Thiết lập biến môi trường `PORT` với giá trị mặc định là `3000`.
- **`COPY package*.json ./`**: Sao chép các tệp quản lý thư viện trước nhằm tận dụng cơ chế Docker Build Cache.
- **`RUN npm install --production`**: Cài đặt các dependencies cần thiết cho môi trường chạy ứng dụng.
- **`COPY . .`**: Sao chép toàn bộ mã nguồn ứng dụng vào thư mục làm việc `/app`.
- **`EXPOSE 3000`**: Khai báo cổng 3000 mà container lắng nghe.
- **`CMD ["node", "server.js"]`**: Lệnh khởi chạy ứng dụng dưới dạng exec form (chuẩn POSIX signal handling).

---

## 4. Hướng dẫn thực thi & Kiểm tra

### Bước 1: Build Docker Image
Mở terminal tại thư mục dự án và chạy lệnh sau:
```bash
docker build -t hw-backend:v1 .
```

### Bước 2: Chạy Docker Container
Khởi chạy container ở chế độ nền (detached mode), đặt tên container là `app-single-stage` và ánh xạ cổng máy chủ `8080` vào cổng `3000` của container:
```bash
docker run -d --name app-single-stage -p 8080:3000 hw-backend:v1
```

### Bước 3: Kiểm tra trạng thái Container
Kiểm tra container đang chạy:
```bash
docker ps
```
> **Kết quả mong đợi:** Container `app-single-stage` có trạng thái `Up` (ví dụ: `Up About a minute`).

### Bước 4: Kiểm tra Logs của Container
Xem nhật ký chạy ứng dụng:
```bash
docker logs app-single-stage
```
> **Kết quả mong đợi:** Hiển thị thông báo:
> ```text
> Server listening on port 3000
> ```

### Bước 5: Kiểm tra phản hồi API bằng cURL
Gửi HTTP request tới cổng 8080:
```bash
curl -i http://localhost:8080/
```
> **Kết quả mong đợi:** Mã HTTP `200 OK` kèm dữ liệu JSON:
> ```http
> HTTP/1.1 200 OK
> Content-Type: application/json; charset=utf-8
> ...
> 
> {"status":"success","message":"Hello from Docker Single-stage!"}
> ```

---

## 5. Dọn dẹp môi trường (Optional)
Khi hoàn tất quá trình kiểm tra, có thể dừng và xoá container:
```bash
docker stop app-single-stage
docker rm app-single-stage
```
Xóa image nếu không còn sử dụng:
```bash
docker rmi hw-backend:v1
```
