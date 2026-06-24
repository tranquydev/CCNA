# Day 17: Application P2

## I. Bản chất và Vai trò của Giao thức DNS

### 1. Định nghĩa & Lý do ra đời

* **Định nghĩa:** DNS (Domain Name System) là giao thức thuộc tầng ứng dụng (Layer 7), có chức năng làm hệ thống phân giải tên miền.
* **Vai trò:** Hoạt động như một "danh bạ điện thoại". Máy tính chỉ hiểu dữ liệu dạng số (IP), con người chỉ dễ nhớ dữ liệu dạng chữ (Domain name). DNS làm nhiệm vụ biên dịch/ánh xạ giữa hai thế giới này.
* **Tầm quan trọng:** Nếu không có DNS, mỗi khi muốn truy cập một trang web, con người bắt buộc phải thuộc lòng địa chỉ IP (Ví dụ: Thay vì gõ `dantri.com.vn`, phải gõ trực tiếp địa chỉ IP `42.113.x.x` lên trình duyệt).

### 2. Nguyên lý cấu hình

Để một website (Ví dụ: `ccna.com` chạy trên Web Server `192.168.1.20`) hiển thị được, kỹ sư phải khai báo thông tin trên DNS Server (IP: `192.168.1.30`). DNS Server sẽ lưu bản ghi (Record):

> `Domain Name: ccna.com` $\longleftrightarrow$ `Target IP Address: 192.168.1.20`

## III. Ma trận 6 bước vận hành ngầm (HTTP $\rightarrow$ DNS $\rightarrow$ ARP)

Khi người dùng tại PC2 mở trình duyệt và gõ `ccna.com`, toàn bộ hệ thống xảy ra tiến trình "đóng băng" (Hold) dữ liệu liên tục để tra cứu:

1. **Bước 1: Khởi tạo gói tin Web (HTTP Request) $\rightarrow$ Bị đóng băng:**
Gói tin Web được tạo ra với `IP Source: 192.168.1.2`, nhưng ô `IP Destination` bị khuyết. Gói tin bị giữ lại (Hold) trong hàng đợi cạc mạng. Hệ thống kích hoạt DNS đi trước.
2. **Bước 2: Khởi tạo gói tin DNS Request $\rightarrow$ Bị đóng băng lần 2:**
Tạo gói tin DNS hướng tới máy chủ `192.168.1.30`. Nhưng lúc này máy tính chưa biết địa chỉ MAC của DNS Server là gì. Gói tin DNS bị giữ lại tiếp.
3. **Bước 3: Phát bản tin ARP Request:**
PC2 phóng bản tin `ARP Request` (Broadcast: `FFFF.FFFF.FFFF`) để tìm MAC của DNS Server. Switch thực hiện `Flooding`. DNS Server trả về `ARP Reply` (Unicast) chứa địa chỉ MAC. PC2 học thành công MAC của DNS Server.
4. **Bước 4: Giải phóng DNS & Nhận IP:**
PC2 gửi bản tin DNS tới DNS Server. DNS Server trả về thông báo: "ccna.com có IP là 192.168.1.20". PC2 điền IP này vào ô trống của gói tin Web ban đầu.
5. **Bước 5: Phát bản tin ARP thứ hai:**
Gói tin Web đã có IP Đích nhưng vẫn thiếu MAC của Web Server. PC2 tiếp tục cho "đóng băng" gói tin Web, phóng tiếp 1 bản tin ARP Request nữa để hỏi MAC của Web Server. Web Server phản hồi `ARP Reply`.
6. **Bước 6: Thông mạng:**
Gói tin Web được đóng gói đầy đủ (IP Đích + MAC Đích), phóng thẳng đến Web Server. Web Server trả về mã HTML. Trình duyệt hiển thị website.

## IV. Quy trình cấu hình chi tiết trên Packet Tracer

* **Bước 1 (IP Tĩnh):** Đặt IP tĩnh cho DNS Server (`192.168.1.30`) và Web Server (`192.168.1.20`). Subnet: `255.255.255.0`, Gateway: `192.168.1.100`.
* **Bước 2 (DNS Service):** Vào `DNS Server` ➔ `Services` ➔ `DNS` ➔ Bật `On`. Điền tên miền (Ví dụ: `hoangvd.com`) và trỏ vào IP Web Server (`192.168.1.20`) ➔ `Add`.
* **Bước 3 (Web Service):** Vào `Web Server` ➔ `Services` ➔ `HTTP` ➔ `Edit` file `index.html`. Thay đổi nội dung tiêu đề thành lời chào riêng của bạn ➔ `Save`.
* **Bước 4 (Client PC):** Gán cạc mạng PC sang chế độ `DHCP`. PC sẽ tự học địa chỉ DNS Server. Mở trình duyệt, gõ `hoangvd.com` để kiểm tra.

## V. Thủ thuật điều khiển Packet Tracer

* **Nhân bản thiết bị:** Giữ `Ctrl` + Click biểu tượng thiết bị ➔ Click chuột liên tục lên màn hình để đặt hàng loạt.
* **Cắm cáp nhanh:** Giữ `Ctrl` + Click biểu tượng sợi cáp ➔ Cắm liên tục nhiều cổng.
* **Xóa nhanh:** Nhấn `Delete` hoặc dùng công cụ dấu X (cắt) ➔ Nhấn `ESC` để quay lại chế độ chuột thường.

## VI. Bài tập về nhà

* Xây dựng sơ đồ mạng: 1 DHCP Server, 1 DNS Server, 1 Web Server, 1 Router, 2 PC trạm.
* Cấu hình tên miền theo họ tên.
