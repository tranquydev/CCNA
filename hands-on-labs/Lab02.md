# Lab 02: Cấu Hình Bảo Mật Mật Khẩu Đa Lớp & Biểu Ngữ Cảnh Báo (Banner MOTD)

## 1. Yêu cầu bài thực hành (Kịch bản Lab)
* **Công cụ giả lập:** Cisco Packet Tracer (Router dòng **2811**).
* **Nhiệm vụ cấu hình:**
  1. Đổi tên Router sang tên viết tắt bằng câu lệnh rút gọn (`conf t` -> `hostname TRANQUY`).
  2. Triển khai mật khẩu mã hóa bảo mật cửa ngõ đặc quyền bằng lệnh `enable secret`.
  3. Triển khai mật khẩu khóa cổng kết nối vật lý trực tiếp bằng cách cấu hình đường `line console 0`.
  4. Cấu hình biểu ngữ cảnh báo pháp lý chống xâm nhập (`banner motd`) đập thẳng vào mắt người dùng trước khi đăng nhập.
* **Yêu cầu minh chứng:** Gõ `exit` thoát hẳn ra ngoài màn hình đen kịch khung để kiểm tra quy trình bảo mật 3 lớp nghiêm ngặt.

---

## 2. Các câu lệnh cấu hình đã thực thi

Quá trình truy cập vào chế độ toàn cục và thiết lập các lớp bảo mật bảo vệ thiết bị:

```ios
! --- Bước 1: Vào chế độ cấu hình toàn cục bằng lệnh viết tắt ---
Router> en
Router# conf t

! --- Bước 2: Đổi tên thiết bị ---
Router(config)# hostname TRANQUY

! --- Bước 3: Đặt mật khẩu mã hóa cho Privileged EXEC Mode ---
TRANQUY(config)# enable secret <mật_khẩu_đặc_quyền_của_bạn>

! --- Bước 4: Đặt mật khẩu cho cổng Console vật lý ---
TRANQUY(config)# line console 0
TRANQUY(config-line)# password <mật_khẩu_console_của_bạn>
TRANQUY(config-line)# login
TRANQUY(config-line)# exit

! --- Bước 5: Cấu hình biểu ngữ cảnh báo Banner MOTD ---
TRANQUY(config)# banner motd # CANH BAO: Day la thiet bi cua Ngan Hang. Moi hanh vi xam nhap trai phep se bi truy to phap luat! #
TRANQUY(config)# end
TRANQUY# exit

```

---

## 3. Kết quả minh chứng quy trình bảo mật 3 lớp (Screenshot)

Hình ảnh giao diện CLI thực tế sau khi gõ thoát hệ thống và đăng nhập lại, minh chứng cấu hình chạy chính xác 100%:

* **Lớp 1 (Banner MOTD):** Đập thẳng thông điệp cảnh báo pháp lý của Ngân hàng ra màn hình CLI.
* **Lớp 2 (Console Password):** Xuất hiện dòng chữ `User Access Verification` và chặn không cho lọt vào User Mode cho đến khi nhập đúng mật khẩu cổng Console.
* **Lớp 3 (Privileged Password):** Khi gõ lệnh viết tắt `en`, hệ thống tiếp tục khóa và đòi mật khẩu đặc quyền lớp thứ hai mới cho phép tiến vào chế độ quản trị dấu `#`.

```
