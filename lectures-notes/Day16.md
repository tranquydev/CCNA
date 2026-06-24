# Day 16: L4 and Application

## I. Tổng kết Logic Layer 2 & Giao thức ARP
* **Cơ chế Switch (2 bước):**
  1. **Học (Learning):** Đọc `MAC Source` để ánh xạ vào bảng `Mac-Address-Table` (Aging Time: 300s).
  2. **Chuyển mạch (Forwarding):** Đọc `MAC Destination`. Nếu chưa biết ➔ **Flooding** (Phát tán). Nếu đã biết ➔ **Unicast** (Gửi thẳng).
* **Giao thức ARP:** Giải quyết bài toán "Biết IP, chưa biết MAC" bằng cách phát bản tin **ARP Request** (MAC Broadcast: `FFFF.FFFF.FFFF`) đi hỏi toàn mạng, sau đó lưu kết quả vào **ARP Table** (thời gian lưu 10 phút).

## II. Kiến trúc Tầng giao vận Layer 4 (TCP vs UDP)
Tầng giao vận đóng vai trò điều phối luồng dữ liệu thô từ ứng dụng.

| Đặc điểm | TCP (Transmission Control Protocol) | UDP (User Datagram Protocol) |
| :--- | :--- | :--- |
| **Độ tin cậy** | Rất cao (Có xác nhận, có truyền lại). | Thấp (Không kiểm soát, mất là mất). |
| **Cơ chế** | Bắt tay 3 bước (3-way handshake). | Phóng dữ liệu ngay, không kết nối trước. |
| **Ứng dụng** | Web (HTTP/HTTPS), Mail, SSH/Telnet. | VoIP, Livestream, Game Online. |
| **Đặc thù** | Đánh số thứ tự (Sequence Number). | Không phân mảnh, ưu tiên Real-time. |

* **Cổng (Port) ứng dụng:** Dùng để định danh phần mềm trên máy tính:
  * `Port 80`: HTTP.
  * `Port 443`: HTTPS.
  * `Port 23`: Telnet (Không mã hóa).
  * `Port 22`: SSH (Bảo mật).

## III. Thực hành cấu hình DHCP (Layer 7)
DHCP Server tự động cấp: IP, Subnet Mask, Default Gateway, DNS.

### 1. Quy hoạch sơ đồ
* **Subnet:** `192.168.1.0/24`.
* **IP tĩnh (VIP):** `192.168.1.1` - `192.168.1.99`.
* **DHCP Pool:** `192.168.1.100` trở đi.

### 2. Các bước cấu hình trên Packet Tracer
#### ➊ Thiết lập DHCP Server
* **Static IP:** `192.168.1.10`.
* **Services ➔ DHCP:** `On`.
* **Cấu hình:** `Gateway: 192.168.1.100`, `DNS: 8.8.8.8`, `Start IP: 192.168.1.100`.

#### ➋ Cấu hình Router (CLI)
```ios
Router# configure terminal
Router(config)# interface GigabitEthernet 0/0
Router(config-if)# no shutdown
Router(config-if)# ip address dhcp    ! Ép Router đi xin IP động
```

#### ➌ Kiểm tra

* **Router:** `show ip interface brief` ➔ Cột Method hiển thị `DHCP`.
* **PC:** Chuyển từ Static sang `DHCP` ➔ Tự nhận IP từ dải `.1.101`.

## IV. Bài tập về nhà

1. Xây dựng topo: 1 DHCP Server, 1 Switch, 1 Router, 2 PC.
2. Cấu hình DHCP dải `.1.100` trở lên.
3. Chụp ảnh xác nhận PC nhận đúng IP động.
```
