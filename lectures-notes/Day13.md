# Day 13: Lab IP

## I. Bản chất Quản trị và Cách tính nhanh Subnet Mask

### 1. Bản chất kỹ thuật
Trong suốt chuỗi bài học chia mạng con (Subnetting) vừa qua, chúng ta hoàn toàn sử dụng số hiệu **Prefix (như /24, /26)** để khóa cứng phần mạng. Prefix là thông số trực quan nhất cho tư duy con người. 

Tuy nhiên, trong cú pháp dòng lệnh của các thiết bị Cisco đời cũ hoặc hệ điều hành Windows cũ, phần mềm bắt buộc kỹ sư phải nhập vào một dải số tương đương gọi là **Subnet Mask**. Do đó, Subnet Mask sinh ra chỉ để phục vụ cho đúng cú pháp gõ lệnh trên thiết bị. 
*(Các hệ điều hành mới như Windows 11 hay Cisco IOS-XE hiện nay đã cho phép loại bỏ hoàn toàn Subnet Mask và gõ trực tiếp số hiệu Prefix).*

### 2. Cấu trúc phần cứng
Subnet Mask có kiến trúc giống hệt địa chỉ IPv4 gồm **32 bit nhị phân** chia làm 4 Octet ngăn cách bởi dấu chấm.



#### 📐 Quy tắc 2 bước tính Subnet Mask từ chỉ số Prefix cho trước:
* **Bước 1:** Căn cứ theo số hiệu Prefix bằng bao nhiêu, ta gán bấy nhiêu bit đầu tiên (tính từ trái qua phải) bằng **số 1**.
* **Bước 2:** Toàn bộ tất cả các bit còn lại trong tổng số 32 bit gán bằng **số 0**. Sau đó quy đổi cụm nhị phân của từng Octet về lại dải thập phân.

#### 🛠️ Ví dụ minh họa (Đã hoàn thành nhẩm tại lớp):
* **Với nhãn mạng `/24`:** 24 bit đầu gán bằng 1 (tương đương 3 Octet đầu full 1 kịch khung `11111111`) ➔ Quy đổi về thập phân là: **`255.255.255.0`**
* **Với nhãn mạng `/23`:** Khuyết thiếu 1 bit 1 ở Octet thứ 3 so với dải /24 ➔ Lấy số tối đa 255 - 2^0 (1) = 254 ➔ **`255.255.254.0`**
* **Với nhãn mạng `/26`:** 24 bit đầu full 1, Octet thứ 4 chứa 2 bit 1 đứng đầu (`11000000` ➔ 128 + 64 = 192) ➔ **`255.255.255.192`**

---

## II. Cách tính nhanh Wildcard Mask (Mặt nạ nghịch đảo)

* **Bản chất:** Là thông số toán học nghịch đảo hoàn toàn của Subnet Mask, được dùng phổ biến để làm bộ lọc điều kiện chặn cấm lưu lượng dữ liệu (Access Control List - ACL) hoặc khai báo định tuyến động OSPF ở học phần nâng cao.
* **Công thức nhẩm siêu tốc:**  `Wildcard Mask = 255.255.255.255 - Subnet Mask`
  *(Kỹ sư thực hiện phép trừ tịnh tiến độc lập trên từng Octet một).*

### 🛠️ Ví dụ minh họa: Tìm Wildcard Mask của khối mạng `/26`
* Có Subnet Mask tương ứng là: `255.255.255.192`
* Tiến hành phép tính trừ độc lập:
  * Octet 1, 2, 3: `255 - 255 = 0`
  * Octet 4: `255 - 192 = 63`
* ➔ **Wildcard Mask chuẩn: `0.0.0.63`**

---

## III. Quy ước gán IP cho các Thiết bị trong một Mạng LAN

Kỹ sư trưởng khi thiết kế bản vẽ sơ đồ mạng doanh nghiệp phải tuân theo quy ước phân bổ dải số IP quốc tế sau để đồng bộ toàn bộ hệ thống:

1. **Ranh giới vật lý:** Mỗi mạng LAN nội bộ hoặc một đường mạng WAN kết nối giữa các Router phải nằm ở một dải Subnet mạng con khác nhau hoàn toàn.
2. **Cổng Giao tiếp của Router (Interface L3):** Luôn luôn được ưu tiên gán địa chỉ **IP Host đầu tiên, khả dụng nhỏ nhất** trong Subnet đó (First Usable IP). IP này đồng thời được khai báo làm **Default Gateway (Cổng cửa ngõ mặc định)** cho toàn bộ máy con trong phân vùng mạng đó nhìn hướng ra internet.
3. **Cổng quản trị của Switch (Interface Virtual VLAN 1):** Luôn luôn được quy ước gán địa chỉ **IP Host thứ hai** (Second Usable IP) liền sau cổng Router để phục vụ kỹ sư đăng nhập quản trị từ xa (Telnet/SSH).
4. **Máy trạm Client (PC, Laptop, Server):** Được phân bổ gán lùi dần từ địa chỉ **IP Host cuối cùng, khả dụng lớn nhất** trở lên (Last Usable IP).

---

## IV. Quy trình gõ lệnh cấu hình Bài Lab Chia mạng nâng cao 11.9.3

### 1. Phân rã bài toán VLSM mẫu chạy trên sơ đồ
Đề bài cấp dải mạng mẹ gốc là `172.16.6.0/24`. Nhẩm tịnh tiến Bước Nhảy (Block size = 32) chia nhỏ cấp cho 2 phòng ban:
* **Subnet Room 114 (Cần 27 máy -> gán nhãn `/27`):** `172.16.6.0/27` (Dải IP chạy từ .6.0 đến .6.31, IP đầu: `.6.1`, IP cuối: `.6.30`).
* **Subnet Room 279 (Cần 25 máy -> gán nhãn `/27`):** `172.16.6.32/27` (Dải IP chạy từ .6.32 đến .6.63, IP đầu: `.6.33`, IP cuối: `.6.62`).

### 2. Tiến trình gõ lệnh CLI nạp cấu hình thực tế

#### ➊ Khai báo cấu hình địa chỉ IP trên các cổng của Router PR1:
```ios
PR1# configure terminal
PR1(config)# interface GigabitEthernet 0/0
PR1(config-if)# no shutdown
PR1(config-if)# ip address 172.16.6.1 255.255.255.224

PR1(config-if)# interface GigabitEthernet 0/1
PR1(config-if)# no shutdown
PR1(config-if)# ip address 172.16.6.33 255.255.255.224
PR1(config-if)# end

```

#### ➋ Khai báo IP quản trị ảo và Default Gateway trên Switch Room_312:

Switch là thiết bị Layer 2, bắt buộc đặt IP quản trị ẩn vào cổng ảo VLAN 1 và gán lệnh trỏ đường default-gateway hướng lên cổng Router vật lý để máy tính có thể định tuyến ra ngoài:

```ios
Room_312# configure terminal
Room_312(config)# interface vlan 1
Room_312(config-if)# no shutdown
Room_312(config-if)# ip address 172.16.6.66 255.255.255.240
Room_312(config-if)# exit
Room_312(config)# ip default-gateway 172.16.6.65

```

#### ➌ Nạp thông số tĩnh trên máy trạm PC Client (Giao diện đồ họa GUI):

* Click chọn máy **PC_D** (Thuộc phân dải mạng Room 312) ➔ Chọn tab **Desktop** ➔ Mục **IP Configuration**.
* **Ô IPv4 Address:** Điền địa chỉ IP Host cuối cùng khả dụng của khối: `172.16.6.78` (Dải gốc của phòng là `.6.64/28`, có Broadcast là `.79`, suy ra IP cuối khả dụng là `.78`).
* **Ô Subnet Mask:** Nhập dải số mặt nạ mạng mạng: `255.255.255.240`
* **Ô Default Gateway:** Nhập địa chỉ IP cổng Router che chở bảo vệ: `172.16.6.65`

---

## V. Bài tập thực hành bắt buộc Day 13

* Thực hiện hoàn thiện, sửa lỗi tự động và đẩy tiến độ đạt mức Full 100% Completion cho 2 bài Lab tính điểm tự động trên phần mềm Packet Tracer gồm: **Bài Lab 11.9.3** và **Bài Lab 11.10.1**.
* Thực hiện gõ lệnh `write` (hoặc `wr`) để bảo toàn lưu file cấu hình trong bộ nhớ NVRAM, chụp hình ảnh thông báo hoàn thành 100% gửi lên nhóm lớp để lấy điểm điều kiện thi kết thúc Học phần.
