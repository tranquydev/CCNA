# Day 18: Application P3

## I. Bản chất và Quy trình vận hành của Dịch vụ Mail Server

### 1. Vạch trần hiểu lầm tai hại
Nhiều người dùng phổ thông thường nghĩ rằng khi PC0 gửi thư cho PC1, dữ liệu sẽ chạy đường thẳng xuyên internet sang cạc mạng đối phương. **Bản chất kỹ thuật:** Không bao giờ có cơ chế truyền thẳng đó. Mọi tiến trình truyền nhận Mail bắt buộc phải chạy qua một trạm trung chuyển trung gian gọi là **Mail Server** (Ví dụ: Gmail Server).

### 2. Kịch bản dòng chạy gói tin Mail (2 chặng độc lập)
* **Chặng 1 (Gửi thư - SMTP):** 
  * PC0 (`pc0@gmail.com`) soạn thảo văn bản và nhấn Gửi. Dữ liệu phóng lên nạp vào bộ nhớ lưu trữ của PC0 nằm trên Mail Server. 
  * Giao thức điều phối chặng đi này là **SMTP** (Simple Mail Transfer Protocol). 
  * *Lưu ý:* Khi nhận thông báo "Send Success", nghĩa là thư đã lọt vào Mail Server an toàn, chứ máy PC1 vẫn chưa nhận được gì.
* **Chặng 2 (Tải thư về - POP3/IMAP):** 
  * Mail Server đọc địa chỉ nhận (`pc1@gmail.com`) và copy lá thư từ hộp thư đi của PC0 sang ngăn hộp thư đến của PC1 nằm trên ổ cứng Server.
  * Máy PC1 không tự động hiển thị thư. Khi người dùng tại PC1 chủ động ấn nút **Receive** (Nhận thư), máy PC1 kích hoạt giao thức **POP3** (hoặc IMAP) phóng lệnh lên Mail Server để rút (tải) file thư từ bưu cục về hiển thị lên máy trạm.

> 🎯 **Quy tắc cô lập lỗi:** Nếu PC0 báo gửi thành công mà PC1 không đọc được thư, 90% lỗi thuộc chặng 2: PC1 hỏng cạc mạng, dính lỗi Gateway hoặc gõ sai DNS Server khiến không phân giải được `gmail.com`.

---

## II. Bản chất công nghệ Đám mây (Cloud Computing)
* **Về mặt hạ tầng:** Cloud không phải thứ gì đó vô hình trên trời. Đó thực chất là các trung tâm dữ liệu (Data Center) với hàng nghìn Server phần cứng hữu hình thuộc các tập đoàn lớn (Google, Apple, Microsoft) cắm vào môi trường internet toàn cầu.
* **Tại sao gọi là Đám mây?** Như việc nhìn lên trời ở đâu cũng thấy mây, công nghệ Cloud cho phép kỹ sư dù đứng ở bất kỳ quốc gia nào, chỉ cần có internet là có thể đăng nhập, tải/rút dữ liệu từ ổ cứng máy chủ đặt ở nửa kia bán cầu một cách trong suốt.

---

## III. Dịch vụ Máy chủ Sao lưu TFTP (Backup & Restore)
Thiết bị mạng (Switch/Router) chứa hàng nghìn dòng lệnh cấu hình phức tạp. Nếu thiết bị cháy nổ bo mạch mà không có bản lưu trữ, kỹ sư sẽ mất hàng tuần để cấu hình lại.

### 1. Lệnh Sao lưu (Backup - Running Config sang TFTP Server)
Giả định: IP Switch (`192.168.1.50`), IP TFTP Server (`192.168.1.60`).
```ios
Switch# copy running-config tftp:
Address or name of remote host []? 192.168.1.60
Destination filename [Switch-confg]? Switch1_TT.cfg
!! [OK - 1024 bytes copied in 0.5 secs]
```

### 2. Lệnh Cứu hộ (Restore - Từ TFTP đổ về Switch mới)

Khi thay Switch mới, kỹ sư nạp lại file cấu hình từ Server:

```ios
Switch# copy tftp: running-config
Address or name of remote host []? 192.168.1.60
Source filename []? Switch1_TT.cfg
Destination filename [running-config]? [Enter]
```

*(Ngay sau đó, thiết bị sẽ tự động load toàn bộ cấu hình cũ và thay đổi tên thiết bị theo file đã lưu).*

---

## IV. Thực hành cấu hình Mail Server trên Packet Tracer

### 1. Cấu hình trên Mail Server

* **Tab Services ➔ Email:** Bật `On` các giao thức SMTP và POP3.
* **Domain Name:** Nhập `gmail.com` ➔ Ấn **Set**.
* **Tạo Account:** Nhập User (PC1, PC2) và Pass, nhấn nút dấu cộng (+) để tạo.

### 2. Cấu hình trên PC Client (PC1)

* **Tab Desktop ➔ Email:**
* **Your Name / Email Address:** `PC1`, `PC1@gmail.com`.
* **Incoming/Outgoing Mail Server:** `gmail.com`.
* **User Name / Password:** `PC1`, `cisco` ➔ Nhấn **Save**.


---
