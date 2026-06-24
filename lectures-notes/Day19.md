# Day 19: Telnet and ôn tập

## I. Bản chất Phương thức Quản trị từ xa (Telnet)
### 1. Phân biệt phương thức quản trị
* **Console (Quản trị trực tiếp):** Kỹ sư phải mang Laptop cắm dây cáp vật lý trực tiếp vào cổng Console phần cứng của thiết bị. 
  * *Nhược điểm:* Phải ngồi trong phòng Server (tiếng ồn, nhiệt độ cao), không thể xử lý từ xa.
* **Remote Management (Quản trị từ xa):** Cho phép ngồi bất kỳ đâu trên thế giới (nhà, quán cafe), chỉ cần thiết bị thông mạng internet là có thể đăng nhập gõ lệnh điều khiển Switch/Router ở cơ quan.
  * *Giao thức gồm:* Telnet (kém bảo mật) và SSH (bảo mật cao).

### 2. Bản chất của Telnet (Port 23 - TCP)
* **Cơ chế:** Truyền dữ liệu dạng văn bản thô (Plain Text). Mọi ký tự câu lệnh và Password gõ từ máy Client đều không được mã hóa, dễ bị hacker chặn bắt và đọc trộm gói tin.
* **Quy định doanh nghiệp:** Chỉ dùng Telnet trong mạng LAN nội bộ an toàn. Đối với môi trường internet, bắt buộc phải nâng cấp lên **SSH (Port 22)**.

---

## II. Tuyệt kỹ cấu hình Telnet & Giải mã "line vty 0 15"

### 1. Ý nghĩa "line vty 0 15"
* **VTY (Virtual Teletype):** Cổng đường truyền ảo ngầm phục vụ quản trị từ xa.
* **Dải 0-15:** Quy định giới hạn số lượng phiên kết nối (Session) có quyền đăng nhập đồng thời.
* **Toán học phiên:** Dải `0 15` tương đương `15 - 0 + 1 = 16` phiên kết nối song song. 
* **Bảo mật:** Nếu công ty chỉ có 3 nhân sự Admin, kỹ sư nên dùng lệnh `line vty 0 2` để bóp hẹp dải phiên, tránh khe hở cho hacker chiếm quyền.

### 2. Quy trình cấu hình trên Router (Chặng 1)
```ios
Router# configure terminal
Router(config)# line vty 0 15              ! Mở dải cổng ảo
Router(config-line)# transport input telnet ! Ép cổng chỉ đón lưu lượng Telnet
Router(config-line)# password cisco        ! Thiết lập mật khẩu cửa ngõ
Router(config-line)# login                 ! Ép thiết bị bắt nhập mật khẩu
Router(config-line)# exit
Router(config)# enable secret cisco        ! BẮT BUỘC: Đặt pass cho Privileged EXEC Mode
```

* **⚠️ Bẫy lỗi "Nợ mật khẩu đặc quyền":** Nếu thiếu lệnh `enable secret`, bạn sẽ Telnet vào được User Mode nhưng bị chặn ở lệnh `enable` với lỗi `% No password set`. Đây là quy tắc bảo mật tối thượng của Cisco.

---

## III. Chặng 2: Kiểm chứng kết nối từ PC Client

Đây là bước xác nhận hệ thống đã thông suốt (thực hiện trong Command Prompt - `cmd`):

1. **Truy cập:** Gõ lệnh `telnet 192.168.1.1` (IP của Router).
2. **Đăng nhập:** Màn hình hiện `Password: `. Gõ `cisco` (không hiện ký tự) ➔ `Enter`.
3. **Nâng quyền:** Tại dấu nhắc `Router>`, gõ `enable` ➔ Nhập mật khẩu `cisco` của lớp đặc quyền.
4. **Thành công:** Dấu nhắc chuyển thành `Router#`. Bạn đã chính thức nắm quyền điều khiển từ xa.

---

## IV. Định hướng Nghề nghiệp Kỹ sư CNTT/Viễn thông

Giảng viên vạch ra sơ đồ ma trận nhân sự để học viên định hướng bến đỗ sự nghiệp:

### 1. Khối trường phái: Kỹ sư Triển khai (Post-Sale / SI Engineer)

* **Đơn vị làm việc:** Các tập đoàn Tích hợp hệ thống (FPT-IS, Sao Bắc Đẩu, SVTech, Amigo, Detin...).
* **Nhiệm vụ:** Thiết kế bản vẽ, lắp đặt phần cứng, đấu nối cáp, gõ lệnh cấu hình hệ thống mới cho khách hàng, ngân hàng, cơ quan nhà nước.
* **Ưu điểm:** Level kiến thức thăng hạng thần tốc do va chạm hàng trăm topo mạng khác nhau, cấu hình nhiều thiết bị cao cấp (Cisco, Juniper, Fortinet). Thời gian tự do, linh hoạt.
* **Nhược điểm:** Cực kỳ vất vả, phải đi tỉnh công tác xa, trực đêm, bê vác thiết bị nặng.

### 2. Khối trường phái: Kỹ sư Vận hành (Operation / Admin / IT Support)

* **Đơn vị làm việc:** Khối ngân hàng (VCB, BIDV, Techcombank), nhà mạng (Viettel, VNPT, FPT Telecom), dịch vụ nội dung (VNG, Garena).
* **Nhiệm vụ:** Trực ca văn phòng, giám sát băng thông, xử lý sự cố lỗi phát sinh, gõ lệnh thêm bớt cổng.
* **Ưu điểm:** Văn phòng sạch sẽ, ổn định, ít di chuyển, phúc lợi cao.
* **Nhược điểm:** Kiến thức dễ bị mai một do làm việc trên topo cố định, áp lực đền bù trách nhiệm cực lớn (gõ sai một lệnh sập hệ thống thanh toán ngân hàng là thảm họa).

> 🧭 **Lời khuyên vàng:** Lộ trình tối ưu cho sinh viên mới ra trường là **làm vị trí Triển khai 2-3 năm đầu** để tích lũy vốn sống và kiến thức thực chiến. Sau đó mới "lật cánh" sang vị trí Vận hành trong Ngân hàng hoặc làm Cloud/Security để đạt mức lương 20-30 triệu VNĐ.

---


* **Bài tập :** Xây dựng topo mạng, gõ `line vty 0 15`, cấu hình Telnet thành công từ PC sang Router.
y đã bao gồm đầy đủ mọi chi tiết từ Chặng 2 (kết nối Telnet) cho đến những lời định hướng nghề nghiệp "xương máu" mà thầy giáo đã giảng. Chúc bạn ôn tập thật tốt cho buổi thi cuối kì vào tối thứ Tư nhé! Bạn đã ôn qua các bộ lệnh `show` bảng ARP và MAC chưa? Nếu muốn tôi giúp bạn tổng hợp lại toàn bộ các lệnh cấu hình từ Day 1 đến Day 19 thành một "Cheat Sheet" bỏ túi, hãy cho tôi biết nhé!
