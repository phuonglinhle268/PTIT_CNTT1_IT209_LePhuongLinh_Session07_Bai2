# Báo Cáo Quản Trị Tường Lửa UFW Cho Cụm Dịch Vụ Multi-Port

## 1. Quy trình cấu hình tường lửa UFW (Terminal Execution Logs)

### Bước 1: Cài đặt tiện ích tường lửa UFW
Cập nhật danh sách gói và cài đặt gói `ufw`:

```bash
plinhle26@devops-vm1:~$ sudo apt update && sudo apt install -y ufw
```

---

### Bước 2: Thiết lập chính sách mặc định (Default Policies)
Cấu hình chặn toàn bộ lưu lượng đi vào máy chủ và cho phép toàn bộ lưu lượng đi ra:

```bash
plinhle26@devops-vm1:~$ sudo ufw default deny incoming
sudo ufw default allow outgoing
Default incoming policy changed to 'deny'
(be sure to update your rules accordingly)
Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly)
```

---

### Bước 3: Cấu hình quy tắc cho các cổng dịch vụ (Firewall Rules)
Thêm các luật mở cổng cho SSH, Web HTTP và Spring Boot; không thêm luật cho cổng MySQL (3306/tcp) để cô lập và bảo vệ cơ sở dữ liệu:

```bash
plinhle26@devops-vm1:~$ sudo ufw allow 22/tcp
Rules updated
Rules updated (v6)
plinhle26@devops-vm1:~$ sudo ufw allow 80/tcp
Rules updated
Rules updated (v6)
plinhle26@devops-vm1:~$ sudo ufw allow 8082/tcp
Rules updated
Rules updated (v6)
```

---

### Bước 4: Kích hoạt tường lửa UFW

```bash
plinhle26@devops-vm1:~$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```

---

### Bước 5: Kiểm tra và xác thực trạng thái chi tiết của UFW

```bash
plinhle26@devops-vm1:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
80/tcp                     ALLOW IN    Anywhere                  
8082/tcp                   ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)             
80/tcp (v6)                ALLOW IN    Anywhere (v6)             
8082/tcp (v6)              ALLOW IN    Anywhere (v6)             
```

---

## 2. Bảng phân tích ma trận chính sách an ninh mạng (Security Matrix)

| Dịch vụ | Cổng mạng / Giao thức | Quy tắc thiết lập | Trạng thái truy cập từ Internet | Mục đích an toàn |
| :--- | :--- | :--- | :--- | :--- |
| **SSH Management** | `22/tcp` | `ALLOW IN` | Cho phép (Anywhere) | Duy trì quyền truy cập quản trị hệ thống từ xa |
| **Web Server (Nginx)** | `80/tcp` | `ALLOW IN` | Cho phép (Anywhere) | Phục vụ người dùng truy cập trang web công khai |
| **Spring Boot Backend** | `8082/tcp` | `ALLOW IN` | Cho phép (Anywhere) | Phục vụ kiểm thử và gọi trực tiếp API từ xa |
| **MySQL Database** | `3306/tcp` | *(Không gán rule)* | **Bị chặn mặc định (`DENY`)** | Cô lập CSDL khỏi Internet, ngăn chặn tấn công quét cổng |
