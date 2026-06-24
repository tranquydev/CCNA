# Day 10: IP P4

## I. Sửa bài tập về nhà Day 9 - Vạch trần lỗi tư duy "Chia bánh trùng dải"

### 1. Bất cập lỗi Overlap IP (Trùng dải số)
Giảng viên bóc tách lỗi sai điển hình của học viên Công khi quy hoạch dải IP cho các phòng ban:
* Phòng B được cấp dải: `192.168.1.96/27` (Chứa dải IP Host từ .96 đến .127).
* Phòng C được cấp dải: `192.168.1.64/26` (Chứa dải IP Host từ .64 đến .127).

**Hậu quả thực tế:** Nhìn vào phân rã nhị phân, dải số của phòng C (.64 đến .127) đã vô tình nuốt trọn toàn bộ không gian địa chỉ của phòng B (.96 đến .127). Trong hạ tầng thực tế, đây gọi là lỗi **Overlap IP**. Khi cấu hình vào thiết bị, Router/Switch sẽ báo lỗi xung đột lập tức, khiến hai phòng này phá sóng dữ liệu của nhau, gây sập toàn bộ hệ thống mạng nội bộ.

### 2. Nguyên lý cắt bánh bất biến
Khi chia nhỏ một dải mạng mẹ lớn, khối mạng nào đã được cắt và gán cho phòng ban trước rồi thì phần không gian đó sẽ biến mất khỏi hệ thống. Kỹ sư chỉ được phép lấy phần diện tích bánh thừa còn lại để tiếp tục phân rã cho các phòng ban phía sau.

---

## II. Bản chất và Kịch bản vận hành nâng cao của VLSM

* **Định nghĩa:** VLSM (Variable Length Subnet Mask) là kỹ thuật chia mạng con có độ dài mặt nạ mạng biến đổi linh hoạt.
* **Mục đích tối thượng:** Giúp gom cụm nhiều địa chỉ IP nhỏ lẻ lại thành một dải Subnet đại diện gọn gàng cho từng phòng ban nhằm áp chính sách bảo mật (**Policy**) trên thiết bị Firewall trung tâm (Ví dụ: Thay vì phải gán lệnh chặn cho từng IP của 100 máy tính phòng IT, ta chỉ cần gán 1 lệnh chặn lên đúng nhãn Subnet của phòng IT, toàn bộ máy con trong phòng sẽ tự động bị khóa).

### Hai điều kiện bắt buộc để đạt chuẩn thiết kế VLSM:
* **Điều kiện 1:** Tính toán tìm chỉ số Prefix sát sườn, vừa vặn nhất với nhu cầu máy trạm (Host demand), không cấp dư thừa quá nhiều gây lãng phí quỹ IP.
* **Điều kiện 2:** Sắp xếp và thực hiện cắt dải số lần lượt theo thứ tự nhu cầu từ lớn xuống nhỏ (Phòng nhiều máy chia trước, phòng ít máy chia sau).

---

## III. Quy trình thực thi Giải thuật Toán mạng VLSM chuẩn Kỹ sư

**Đề bài toán mẫu:** Hạ tầng tổng được cấp dải mạng mẹ gốc là `172.16.6.0/23`. Hãy chia nhỏ dải này cấp cho 4 phòng ban: Phòng A (130 Hosts), Phòng C (31 Hosts), Phòng D (15 Hosts), Phòng B (8 Hosts).



### Tiến trình triển khai bóc tách mượn bit nhị phân tuần tự:

#### 1. Bước 1: Quy hoạch cho Phòng A (130 Hosts - Lớn nhất -> Cần Prefix /24)
* Phân rã Octet thứ 3 của mạng mẹ: Số 6 = 0000.0110. Ranh giới 23 bit dừng tại đây. Để lên /24, ta mượn thêm 1 bit ở vị trí tiếp theo. 1 bit mượn sinh ra 2 khối mạng con /24 tịnh tiến đều 1 đơn vị:
  * **Khối 1 (Mượn bit 0):** 172.16.0000.0110.0000.0000/24 ➔ **172.16.6.0/24** (Cấp cho Phòng A - Chiếm từ .6.0 đến .6.255).
  * **Khối 2 (Mượn bit 1):** 172.16.0000.0111.0000.0000/24 ➔ **172.16.7.0/24** (Giữ làm Kho dữ trữ gốc cho chặng sau).
* ➔ **Kết quả:** Kho bánh thừa chuyển dịch bắt đầu từ mốc `172.16.7.0/24`.

#### 2. Bước 2: Quy hoạch cho Phòng C (31 Hosts - Lớn nhì -> Cần Prefix /26)
* Mang khối thừa `172.16.7.0/24` ra phân rã tiếp. Từ /24 lên /26 mượn thêm 2 bit ở đầu Octet thứ 4, sinh ra 4 khối con /26 tịnh tiến đều 64 đơn vị:
  * **Khối 2a (Mượn cặp bit 00):** 172.16.7.0000.0000/26 ➔ **172.16.7.0/26** (Cấp cho Phòng C - Chiếm từ .7.0 đến .7.63).
  * **Khối 2b (Mượn cặp bit 01):** 172.16.7.0100.0000/26 ➔ **172.16.7.64/26** (Giữ làm Kho dự trữ gốc cho chặng sau).
* ➔ **Kết quả:** Kho bánh thừa chuyển dịch bắt đầu từ mốc `172.16.7.64/26`.

#### 3. Bước 3: Quy hoạch cho Phòng D (15 Hosts - Lớn ba -> Cần Prefix /27)
* Mang khối thừa `172.16.7.64/26` ra chia nhỏ. Từ /26 lên /27 mượn thêm 1 bit, sinh ra 2 khối con /27 tịnh tiến đều 32 đơn vị:
  * **Khối 2b-1 (Mượn bit 0):** 172.16.7.0100.0000/27 ➔ **172.16.7.64/27** (Cấp cho Phòng D - Chiếm từ .7.64 đến .7.95).
  * **Khối 2b-2 (Mượn bit 1):** 172.16.7.0110.0000/27 ➔ **172.16.7.96/27** (Giữ làm Kho dự trữ gốc cho chặng sau).
* ➔ **Kết quả:** Kho bánh thừa chuyển dịch bắt đầu từ mốc `172.16.7.96/27`.

#### 4. Bước 4: Quy hoạch cho Phòng B (8 Hosts - Nhỏ nhất -> Cần Prefix /28)
* Mang khối thừa `172.16.7.96/27` ra chia nốt chặng cuối. Từ /27 lên /28 mượn 1 bit, sinh ra 2 khối con /28 tịnh tiến đều 16 đơn vị:
  * **Khối 2b-2-alpha (Mượn bit 0):** 172.16.7.0110.0000/28 ➔ **172.16.7.96/28** (Cấp cho Phòng B - Chiếm từ .7.96 đến .7.111).
  * **Khối 2b-2-beta (Mượn bit 1):** 172.16.7.0110.1000/28 ➔ **172.16.7.112/28** (Kho dư tồn tài nguyên dự trữ cho tương lai).

---

## IV. Tuyệt kỹ nhẩm nhanh: Phương pháp Bước Nhảy (Block Size Method)

Để tối ưu hóa thời gian và không bị kiệt sức vì phải phân rã chuỗi nhị phân dài dòng, kỹ sư mạng sử dụng tuyệt kỹ nhẩm nhanh bằng **Bước Nhảy (Block Size)**.

* **Công thức cốt lõi:** Mỗi chỉ số Prefix gán vào Octet biến đổi luôn sở hữu một hằng số Bước nhảy cố định toán học:
  * **Block Size (Bước nhảy)** = 2^(32 - Prefix) *(Áp dụng cho Octet chứa ranh giới cắt bít)*

### Bảng quy ước bước nhảy thần tốc (Kỹ sư bắt buộc thuộc lòng):
* Nhãn `/24` ➔ Bước nhảy = 2^(32-24) = 2^8 = **256** (Nhảy đều 1 đơn vị ở Octet thứ 3).
* Nhãn `/25` ➔ Bước nhảy = 2^(32-25) = 2^7 = **128** (Dải số đuôi gồm 2 cụm: .0 và .128).
* Nhãn `/26` ➔ Bước nhảy = 2^(32-26) = 2^6 = **64** (Dải số đuôi gồm 4 cụm: .0, .64, .128, .192).
* Nhãn `/27` ➔ Bước nhảy = 2^(32-27) = 2^5 = **32** (Dải số đuôi nhảy đều cộng thêm 32 đơn vị: .0, .32, .64, .96, .128...).
* Nhãn `/28` ➔ Bước nhảy = 2^(32-28) = 2^4 = **16** (Dải số đuôi nhảy đều cộng thêm 16 đơn vị: .0, .16, .32, .48, .64, .80, .96...).

**Cách ứng dụng thực tế:** Khi cắt bánh cho phòng C cần nhãn `/27` xuất phát từ mốc đang dừng là `.64`, kỹ sư chỉ cần lấy số `64 + Bước nhảy 32 = 96` ➔ Ra ngay địa chỉ của mạng kế tiếp là `.96` trong đúng 1 giây mà không cần đặt bút viết chuỗi nhị phân!

---
