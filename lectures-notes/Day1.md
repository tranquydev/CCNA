# Day 1: Giới Thiệu Network

## I. Thành phần trong hệ thống Mạng (Network Components)

Một hệ thống mạng dữ liệu (Internet) bao gồm 3 nhóm thành phần chính:

### 1. Thiết bị đầu cuối (Host / End Devices)
Là các thiết bị có khả năng tạo ra, nhận và xử lý dữ liệu.
* **Ví dụ:** PC, Laptop, Điện thoại, Máy in, Máy Fax, Máy tính bảng (Tablet), Smart TV.
* **Server (Máy chủ):** Bản chất là một máy tính chuyên dụng cài hệ điều hành và phần mềm riêng để cung cấp các dịch vụ như:
  * **Mail Server:** Cung cấp dịch vụ thư điện tử.
  * **Web Server:** Chứa trang web, xử lý hình ảnh/video.
  * **File Server:** Dùng để lưu trữ dữ liệu (ví dụ: Google Drive).

### 2. Thiết bị trung gian (Intermediary Devices)
Nằm ở giữa các thiết bị đầu cuối, đóng vai trò chuyển tiếp, định tuyến dữ liệu để đảm bảo thông tin đi đúng đích.
* **Các thiết bị phổ biến:**
  * **Switch** (Bộ chuyển mạch)
  * **Router** (Bộ định tuyến)
  * **Modem / Wireless Router** (Modem phát wifi)
  * **Firewall** (Tường lửa)

### 3. Phương tiện truyền dẫn (Network Media)
* **Cáp đồng (Dây mạng/Dây LAN):** Truyền tín hiệu bằng dòng điện. Phổ biến nhất hiện nay là 3 loại:
  * `Cat5`: Băng thông tối đa 100 Mbps.
  * `Cat5e`: Băng thông tối đa 1000 Mbps, khoảng cách truyền tốt nhất dưới 100m.
  * `Cat6`: Băng thông lên tới 10 Gbps (10.000 Mbps), có lõi nhựa ở giữa để giảm suy hao và nhiễu.
* **Cáp quang:** Truyền tín hiệu bằng ánh sáng, dùng để kết nối khoảng cách rất xa (như cáp quang biển, liên tỉnh) vì tốc độ cao và cực kỳ ít bị suy hao.
* **Sóng không dây:** Sóng Wifi, Sóng Bluetooth (kết nối loa, thiết bị ngoại vi), Sóng hồng ngoại (sử dụng trên remote tivi, điều hòa).

> 💡 **Thuật ngữ quan trọng cần nhớ:**
> * **Băng thông (Bandwidth):** Là lượng dữ liệu tối đa có thể truyền tải được trong một đơn vị thời gian.
> * **Suy hao (Attenuation):** Là hiện tượng tín hiệu bị nhỏ/yếu dần đi khi truyền đi xa.

---

## II. Sơ đồ mạng (Topology)

Sơ đồ mạng (Topology) hay còn gọi là bản vẽ hệ thống mạng. Trong thực tế doanh nghiệp sẽ có 2 loại bản vẽ chính:

| Loại Sơ Đồ | Đặc Điểm & Chức Năng | Vai Trò Thực Tế |
| :--- | :--- | :--- |
| **Physical Topology**<br>*(Sơ đồ vật lý)* | Thể hiện vị trí lắp đặt thực tế của thiết bị mạng (ví dụ: đặt ở Tủ rack nào, Ngăn số mấy) và các kết nối dây cáp vật lý. | Giúp kỹ sư mạng dễ dàng tìm kiếm vị trí thiết bị khi gặp sự cố phần cứng hoặc cần cắm lại dây cáp. |
| **Logical Topology**<br>*(Sơ đồ logic)* | Thể hiện mặt chức năng, cấu hình phần mềm (địa chỉ IP của thiết bị, các giao thức định tuyến sử dụng, cổng kết nối). | Giúp kỹ sư xử lý sự cố từ xa, cấu hình thiết bị và điều hướng luồng dữ liệu (traffic). |

---

## III. Bốn tiêu chuẩn cốt lõi trong thiết kế hệ thống mạng

Khi xây dựng hệ thống mạng cho doanh nghiệp lớn, người kỹ sư cần tuân thủ 4 tiêu chuẩn bắt buộc sau:

### 1. Khả năng xử lý lỗi / Dự phòng (Fault Tolerance)
Hệ thống mạng cần có đường truyền hoặc thiết bị dự phòng để khi một đường bị đứt hoặc một thiết bị hỏng, mạng vẫn hoạt động bình thường.
* *Ví dụ:* Công ty thuê đồng thời cả 2 đường truyền mạng của FPT và VNPT để dự phòng cho nhau.

### 2. Khả năng mở rộng (Scalability)
Thiết kế mạng phải tính toán trước năng lực xử lý (capacity) trong kế hoạch 3 - 5 năm tiếp theo để khi số lượng nhân sự hoặc thiết bị tăng lên, hệ thống vẫn đáp ứng tốt mà không cần đập đi xây lại gây lãng phí.

### 3. Chất lượng dịch vụ (QoS - Quality of Service)
Khả năng phân loại và ưu tiên băng thông cho các loại dữ liệu quan trọng hoặc nhạy cảm với độ trễ thời gian (như dữ liệu thoại Voice, video call hội họp trực tuyến).
* *Hình ảnh ẩn dụ:* Giống như việc phân làn đường ưu tiên cho xe cứu thương trong giao thông.

### 4. Bảo mật (Security)
Xây dựng các lớp bảo vệ hệ thống trước các cuộc tấn công mạng từ bên ngoài và bên trong. Thực hiện thông qua việc sử dụng xác thực nhiều lớp (MFA/OTP), phân quyền truy cập và triển khai thiết bị tường lửa (Firewall).
