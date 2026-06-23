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
<img width="825" height="191" alt="image" src="https://github.com/user-attachments/assets/27ef0b98-01ff-4fee-8482-e9fadd450a77" />


