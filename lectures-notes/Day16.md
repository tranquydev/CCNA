# Day 16: Lab IP, Layer 4 (TCP/UDP) & Dịch vụ DHCP

## II. Hệ thống hóa bản chất Layer 2 Switching và ARP (Sau ôn tập)
Sau 30 phút tự học, thầy giáo hệ thống lại ma trận logic Layer 2 để học viên khắc sâu phản xạ:

### 1. Tiến trình hai bước xử lý gói tin bất biến của Switch
* **Bước 1 (Xây dựng bảng MAC - Mac-Address-Table):** Switch đọc trường `MAC Source` trong khung dữ liệu đi vào cổng vật lý để học vị trí. Thiết lập đồng hồ `Aging Time` đếm ngược từ 300 giây (5 phút), tự động xóa nếu cổng dính lỗi `Link Down`.
* **Bước 2 (Chuyển mạch Forwarding):** Switch đọc trường `MAC Destination`. 
  * Nếu khuyết thiếu chưa lưu trong bảng MAC: Switch chạy lệnh `Flooding` nhân bản phát tán đồng loạt ra mọi cổng (trừ cổng nhận vào).
  * Nếu đã lưu: Switch phóng đường `Unicast` chuẩn xác ra đúng 1 Port đích.

### 2. Bản chất cốt lõi của Giao thức ARP
Khi gói tin chính (ICMP/Ping) bị đóng băng do khuyết địa chỉ phần cứng đối phương, thiết bị tự phóng bản tin `ARP Request` mang nhãn quảng bá kịch trần `MAC Destination: FFFF.FFFF.FFFF` (MAC Broadcast) để vượt qua tiến trình `Flooding` của Switch đi truy tìm địa chỉ MAC Đích. Máy trạm/Router lưu ánh xạ IP ⟷ MAC vào Bảng ARP (ARP Table) trong vòng 10 phút.

## III. Kiến trúc Tầng giao vận Layer 4: TCP đối lập UDP
Tầng giao vận (Transport Layer) quản lý cơ chế phân mảnh và điều phối phương thức truyền tải dữ liệu thô từ tầng ứng dụng đưa xuống.

### 1. Giao thức TCP (Tin cậy)
* **Cơ chế:** Bắt buộc chạy "Bắt tay 3 bước" (Three-way handshake) để thiết lập liên kết đồng bộ an toàn trước khi truyền.
* **Tính năng:** Đóng gói nhãn `Sequence Number`. Nếu rơi rụng gói tin, máy nhận yêu cầu máy phát phóng lại (Retransmission), bảo toàn file vẹn nguyên 100%.
* **Ứng dụng:** Web (HTTP/HTTPS), Email, Telnet/SSH.

### 2. Giao thức UDP (Tốc độ)
* **Cơ chế:** Không thiết lập phiên kết nối. Phóng dữ liệu đi ngay lập tức, ưu tiên thời gian thực (Real-time), không quan tâm máy đích có online hay không.
* **Nhược điểm:** Không kiểm lỗi, không truyền lại. Mất gói bỏ qua (Hiện tượng cuộc gọi thoại nghẹt tiếng, giật câu hay livestream vỡ hình là minh chứng của mất gói UDP).
* **Ứng dụng:** Voice IP, Hội nghị truyền hình, Livestream, Game online.

### 3. Định danh ứng dụng bằng mã Port (Well-known)
* Port 80 (TCP): HTTP.
* Port 443 (TCP): HTTPS.
* Port 23 (TCP): Telnet.
* Port 22 (TCP): SSH.

## IV. Thực hành cấu hình dịch vụ cấp IP tự động (DHCP)

### 1. Ý nghĩa
Thay vì gán tay thủ công từng máy (Static IP) dễ sai sót, ta dựng DHCP Server để nó tự động phân bổ: IP, Subnet Mask, Gateway, DNS theo thời hạn thuê (Lease Time).

### 2. Kịch bản bài Lab (Mạng 192.168.1.0/24)
* IP Tĩnh (VIP - Router/Server/Switch): 1.1 đến 1.99.
* DHCP Pool (Cấp tự động): 1.100 trở đi.

### 3. Tiến trình cấu hình (Tóm tắt các lệnh)
* **Trên DHCP Server:** Bật dịch vụ `On` ➔ Gateway: 192.168.1.100 ➔ Start IP: 192.168.1.100 ➔ Save.
* **Trên Router:**
```ios
Router(config)# interface GigabitEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# ip address dhcp   ! Ép Router đi xin IP động từ Server
```

* **Kiểm tra Router:** `show ip interface brief` (Dòng Method hiện DHCP là thành công).
* **Trên PC Client:** Chuyển cạc mạng từ Static sang `DHCP` ➔ Máy tự động nạp IP, Gateway, DNS.

## V. Bài tập về nhà bắt buộc Day 16

* **Topo:** 1 DHCP Server + 1 Switch + 1 Router + 2 PC trạm.
* **Yêu cầu:** Cấu hình dải IP tự động nhảy từ mốc .100 trở đi. Chụp ảnh màn hình máy con nhận IP động gửi lên nhóm lớp.
* **Nhắc nhở:** Ai trốn nộp bài sẽ bị phạt gõ lệnh gấp đôi!
```
