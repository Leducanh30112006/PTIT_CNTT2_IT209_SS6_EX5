# Báo Cáo Bài 5: Thiết kế và Cấu hình dịch vụ Systemd Service cho ứng dụng Java

## 1. Thiết lập tài khoản và Tệp cấu hình

### 1.1. Tạo người dùng hệ thống `java-runner`
```bash
sudo useradd -r -s /usr/sbin/nologin java-runner
```

### 1.2. Tạo tệp dịch vụ Systemd `/etc/systemd/system/java-app.service`
Nội dung tệp cấu hình:
```ini
[Unit]
Description=Java Application Service
After=network.target

[Service]
Type=simple
User=java-runner
ExecStart=/usr/bin/sleep 1000
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

---

## 2. Kích hoạt và Kiểm tra trạng thái dịch vụ

### 2.1. Nạp cấu hình và khởi chạy dịch vụ
```bash
sudo systemctl daemon-reload
sudo systemctl start java-app.service
```

### 2.2. Kết quả kiểm tra trạng thái dịch vụ (`systemctl status java-app.service`)
```bash
$ sudo systemctl status java-app.service
● java-app.service - Java Application Service
     Loaded: loaded (/etc/systemd/system/java-app.service; disabled; vendor preset: enabled)
     Active: active (running) since Tue 2026-10-06 15:40:00 UTC; 5s ago
   Main PID: 31245 (sleep)
      Tasks: 1 (limit: 4613)
     Memory: 200.0K
     CGroup: /system.slice/java-app.service
             └─31245 /usr/bin/sleep 1000

Oct 06 15:40:00 ubuntu-server systemd[1]: Started Java Application Service.
```

Kiểm tra tiến trình thuộc quyền sở hữu của user `java-runner`:
```bash
$ ps -u java-runner -o pid,user,comm,args
    PID USER     COMMAND         COMMAND
  31245 java-ru+ sleep           /usr/bin/sleep 1000
```

**Kết luận:** Dịch vụ `java-app.service` đã khởi chạy thành công ở trạng thái `active (running)` dưới tài khoản hệ thống `java-runner` với chính sách tự động khởi động lại sau 10 giây nếu xảy ra sự cố (`Restart=on-failure`, `RestartSec=10`).
