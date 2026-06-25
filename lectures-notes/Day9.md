# Day 9: IP P3

## I. Sửa bài tập về nhà Day 8 - Phân tích định hoạch dải số phòng ban theo nhu cầu máy trạm

### 1. Phòng ban A (Nhu cầu: 8 Hosts)
* **Tiến trình lập luận:** Ngoài 8 địa chỉ gán cho máy tính nhân viên, hệ thống luôn luôn phải trích thêm 2 địa chỉ chết cố định bắt buộc là địa chỉ mạng (Network ID) và địa chỉ quảng bá (Broadcast IP). Do đó, tổng số lượng IP tối thiểu hệ thống cần tiêu tốn là: `8 + 2 = 10` địa chỉ IP.
* **Đánh giá khối mạng:**  Nếu kỹ sư chọn khối mạng hệ `/29`, không gian IP sở hữu sẽ là 2^3 = 8 địa chỉ. Sau khi trừ đi 2 địa chỉ chết, số lượng IP Host khả dụng thực tế chỉ còn lại `8 - 2 = 6` IP, dẫn đến tình trạng thiếu hụt tài nguyên (6 < 8).
  * Nếu nâng lên khối mạng hệ `/28`, không gian IP sở hữu là 2^4 = 16 địa chỉ. Sau khi trừ đi 2 địa chỉ chết, hệ thống còn lại `16 - 2 = 14` IP Host khả dụng. Khối này hoàn toàn chứa vừa vặn 8 máy trạm và còn dư dung lượng dự phòng cho phòng ban mở rộng nhân sự.
* ➔ **Kết luận:** Phòng ban A bắt buộc phải đặt nhãn **Prefix `/28`**.

### 2. Phòng ban B (Nhu cầu: 100 Hosts)
* **Tiến trình lập luận:** Tổng số lượng IP tối thiểu cần gán cho hệ thống là: `100 + 2 = 102` địa chỉ IP.
* **Đánh giá khối mạng:** Khối mạng hệ `/26` cấp 2^6 = 64 địa chỉ (khả dụng 62 IP) ➔ Không đủ. Khối tiếp theo là hệ `/25` cấp phát không gian 2^7 = 128 địa chỉ. Trừ đi 2 địa chỉ chết, hệ thống còn lại `128 - 2 = 126` địa chỉ IP Host khả dụng, hoàn toàn thỏa mãn chứa được dải 100 máy tính của nhân viên.
* ➔ **Kết luận:** Phòng ban B bắt buộc phải đặt nhãn **Prefix `/25`**.

### 3. Phòng ban C (Nhu cầu: 31 Hosts)
* **Tiến trình lập luận:** Tổng số lượng IP tối thiểu cần gán cho hệ thống là: `31 + 2 = 33` địa chỉ IP.
* **⚠️ Bẫy dải số suýt soát:** Rất nhiều học viên bị nhầm lẫn ở câu này khi chọn khối hệ `/27`. Khối `/27` cấp phát chính xác 2^5 = 32 địa chỉ IP. Tuy nhiên, sau khi trừ đi 2 địa chỉ chết, số lượng IP Host khả dụng gán cho máy trạm chỉ còn đúng 30 IP. Như vậy, phòng ban C sẽ bị thiếu hụt đúng 1 địa chỉ IP cho máy trạm cuối cùng (30 < 31). Để đảm bảo vận hành ổn định, kỹ sư buộc phải nâng kích cỡ khối mạng lên hệ `/26` cấp phát 2^6 = 64 địa chỉ IP (khả dụng 62 IP Host).
* ➔ **Kết luận:** Phòng ban C bắt buộc phải đặt nhãn **Prefix `/26`**.

### 4. Phòng ban D (Nhu cầu: 255 Hosts)
* **Tiến trình lập luận:** Tổng số lượng IP tối thiểu cần gán cho hệ thống là: `255 + 2 = 257` địa chỉ IP.
* **Đánh giá khối mạng:** Vì một Octet đơn trong địa chỉ IPv4 chỉ chứa tối đa 8 bit nhị phân, dẫn đến giới hạn kịch trần số lượng địa chỉ IP Host khả dụng trong một phân vùng mạng LAN thông thường là 254 IP (chạy từ số .1 đến .254). Con số nhu cầu 257 IP đã vượt ngưỡng chịu tải của 1 Octet cuối cùng. Kỹ sư mạng buộc phải thực hiện mượn thêm 1 bit dịch chuyển sang Octet thứ 3, tức là hạ độ dài dải mạng xuống hệ `/23` để cấp phát khối tài nguyên rộng lớn gồm 2^9 = 512 địa chỉ IP.
* ➔ **Kết luận:** Phòng ban D bắt buộc phải đặt nhãn **Prefix `/23`**.

---

## II. Bản chất và Triết lý của Kỹ thuật chia mạng con (Subnetting)

### 1. Tại sao doanh nghiệp bắt buộc phải chia nhỏ mạng con?
Khi một doanh nghiệp đăng ký thuê hoặc mua một dải địa chỉ IP lớn kịch trần từ nhà mạng ISP (Ví dụ nhà mạng cấp cho dải mạng lớn hệ `/24` gồm tổng cộng 256 địa chỉ IP). Nếu người quản trị mạng lười biếng, đổ chung tất cả toàn bộ máy tính của mọi nhân viên trong công ty vào dùng chung một phân vùng mạng duy nhất đó, hệ thống sẽ ngay lập tức đối mặt với hai rủi ro bảo mật và vận hành nghiêm trọng:
* **Hiện tượng bão Broadcast:** Mọi hệ điều hành máy tính khi hoạt động đều liên tục phát ra các gói tin quảng bá Broadcast (như gói tin ARP tìm địa chỉ MAC). Khi số lượng máy tính cắm chung một phân vùng tăng lên, các gói tin Broadcast này sẽ bị Switch nhân bản vô tội vạ và gửi đến mọi cổng, gây ra hiện tượng nghẽn mạch, đơ đường truyền toàn mạng.
* **Không thể thiết lập tường lửa bảo mật riêng biệt:** Khi tất cả ở chung một "phòng", kỹ sư không thể cấu hình các quy tắc chặn hoặc mở quyền truy cập riêng cho từng bộ phận đặc thù.

### 2. Ứng dụng chính sách bảo mật (Policy) lên nhãn Subnet đại diện
Trong một tập đoàn hoặc doanh nghiệp lớn, nhu cầu sử dụng internet của các phòng ban hoàn toàn khác nhau:
* **Phòng Nhân sự (HR):** Cần được mở full quyền truy cập mạng xã hội (Facebook, Zalo, LinkedIn) để phục vụ công tác tuyển dụng, săn đầu người.
* **Phòng Kinh doanh (Sale):** Chỉ cần các dịch vụ cơ bản để gọi điện VoIP, check mail và chat với khách hàng.
* **Phòng Kỹ thuật (IT/Developer):** Buộc phải cấu hình tường lửa chặn toàn bộ mạng xã hội, các trang web giải trí để tập trung làm việc và bảo mật tuyệt đối mã nguồn, tránh rò rỉ dữ liệu.

Thay vì kỹ sư mạng phải đi đến từng bàn làm việc, cấu hình tường lửa thủ công trên từng hệ điều hành máy tính riêng lẻ (rất tốn thời gian, thiếu chuyên nghiệp và dễ sai sót). Giải pháp tối ưu của một kỹ sư trưởng là: **Chia dải IP lớn ban đầu thành các mạng con Subnet độc lập đại diện cho từng phòng ban, sau đó viết các bộ quy tắc bảo mật (Policy) đập trực tiếp lên nhãn Subnet đó trên thiết bị Router/Firewall trung tâm.** Toàn bộ các máy tính trạm Client khi cắm dây mạng vào phân vùng đó sẽ tự động nhận dải IP của phòng và chịu sự thắt chặt hoặc mở rộng quyền truy cập theo đúng quy định chung của phòng ban đó.

---

## III. Bản chất toán học của Phương pháp mượn Bit nhị phân

Bản chất toán học của việc chia mạng con dựa trên mối liên hệ trực quan về gia phả của dòng họ:
* Dòng họ lớn tổng (Họ Trần hệ `/1`): Khóa cứng đúng 1 chữ cái đầu tiên là chữ `Trần`. Tất cả những ai mang họ Trần đều thuộc dòng họ lớn này.
* Các chi họ con (Trần Văn, Trần Vũ, Trần Minh hệ `/2`): Để phân biệt các nhánh nhỏ trong dòng họ, người ta khóa cứng thêm chữ cái thứ hai đứng sau chữ họ gốc.



Đối với địa chỉ IP cũng hoàn toàn tương tự. Khi ta thực hiện nâng cấp thông số Prefix của một khối mạng to từ hệ `/24` lên hệ `/25`, bản chất toán học chạy ngầm trong bo mạch thiết bị là: **Hệ điều hành giữ nguyên hoàn toàn 24 bit gốc thuộc tầng mạng mạng, và tiến hành đi vay mượn thêm đúng 1 bit ở vùng tự do của phần Host Part để làm nhãn khóa cứng mới cho vùng mạng con.**

Vì 1 bit đi vay mượn trong hệ nhị phân chỉ có thể xảy ra độc nhất 2 trạng thái giá trị: Hoặc là mang số `0`, hoặc là mang số `1`. Do đó, 1 khối mạng mẹ kịch trần hệ `/24` luôn luôn được bẻ đôi thành chính xác 2 khối con hệ `/25` độc lập song song:
* **Khối con thứ 1 (Trường hợp mượn bit 0):**
  * Mã nhị phân Octet 4: `192.168.1.0000.0000/25`
  * Biên dịch hệ thập phân ➔ **`192.168.1.0/25`**
* **Khối con thứ 2 (Trường hợp mượn bit 1):**
  * Mã nhị phân Octet 4: `192.168.1.1000.0000/25`
  * Vị trí bit số 7 ($2^7 = 128$) mang số 1 -> Biên dịch hệ thập phân ➔ **`192.168.1.128/25`**

Tương tự như vậy, nếu kỹ sư thực hiện vay mượn thêm **2 bit** ở vùng tự do (Nâng dải mạng lên hệ `/26`), ta có tổng cộng 2^2 = 4 trạng thái tổ hợp nhị phân xảy ra: `00`, `01`, `10`, `11`. Khối mạng mẹ hệ `/24` lúc này sẽ được cắt đều thành chính xác 4 khối con hệ `/26` bằng nhau, sở hữu bước nhảy tịnh tiến cách đều nhau đúng 64 đơn vị:
* Mượn cặp bit `00` ➔ **`192.168.1.0/26`** (Dải số chạy từ .0 đến .63)
* Mượn cặp bit `01` ➔ **`192.168.1.64/26`** (Dải số chạy từ .64 đến .127)
* Mượn cặp bit `10` ➔ **`192.168.1.128/26`** (Dải số chạy từ .128 đến .191)
* Mượn cặp bit `11` ➔ **`192.168.1.192/26`** (Dải số chạy từ .192 đến .255)

---

## IV. Bài toán thực tế đỉnh cao: Thiết kế Quy hoạch mạng VLSM toàn diện

**Đề bài:** Hạ tầng Tổng công ty được nhà mạng ISP cấp phát cho một dải mạng mẹ độc nhất là `192.168.1.0/24`. Doanh nghiệp yêu cầu kỹ sư hạ tầng mạng phải tự tính toán chia nhỏ dải mạng này thành các vùng riêng biệt để cấp phát sát sườn, vừa vặn cho 4 khối văn phòng có nhu cầu máy trạm độc lập sau:
* **Phòng A:** 50 Hosts
* **Phòng B:** 8 Hosts
* **Phòng C:** 25 Hosts
* **Phòng D:** 15 Hosts

### 📐 Thuật toán tư duy "Cắt Bánh" của Kỹ Sư Trưởng (Triết lý VLSM):

kỹ sư mạng khi làm kỹ thuật VLSM (Variable Length Subnet Mask) là: **Luôn luôn phải xếp hàng thứ tự ưu tiên chọn phòng có nhu cầu số lượng máy trạm lớn nhất để cắt bánh trước, rồi lấy phần diện tích bánh thừa còn lại phân rã chia nhỏ dần cho các phòng ban nhỏ hơn sau (Sắp xếp từ lớn đến bé: Phòng A -> Phòng C -> Phòng D -> Phòng B).**


### Tiến trình thực hiện chia cắt dải số chi tiết:

#### 1. Khúc cắt 1: Quy hoạch cho Phòng A (50 Hosts - Ưu tiên số 1)
* **Lập luận tính toán:** Nhu cầu thực tế = 50 máy trạm + 2 địa chỉ chết = 52 IP. Khối mạng nhỏ nhất thỏa mãn lớn hơn hoặc bằng 52 là khối 2^6 = 64 địa chỉ (Tương ứng số bit Host = 6 ➔ Số bit mạng Prefix = 32 - 6 = **`/26`**). Bước nhảy của dải này là 64 đơn vị.
* **Cấp phát tài nguyên:** Lấy ngay khối đầu tiên tính từ mốc số .0 của ổ bánh mì mạng mẹ hệ `/24` gán cho phòng A:
  * ➔ **Subnet chính thức Phòng A: `192.168.1.0/26`**
  * *Bóc tách dải số:* Địa chỉ mạng là `.1.0`, địa chỉ quảng bá Broadcast là `.1.63`. Dải IP Host khả dụng gán cho máy tính nhân viên phòng A chạy liên tục từ **`192.168.1.1` đến `192.168.1.62`**.

#### 2. Khúc cắt 2: Quy hoạch cho Phòng C (25 Hosts - Ưu tiên số 2)
* **Lập luận tính toán:** Sau khi phòng A chiếm mất dải số từ .0 đến .63, khối bánh còn thừa của công ty bắt đầu từ vị trí cột mốc số **`.64`**. Nhu cầu thực tế phòng C = 25 máy trạm + 2 địa chỉ chết = 27 IP. Khối mạng nhỏ nhất thỏa mãn lớn hơn hoặc bằng 27 là khối 2^5 = 32 địa chỉ (Tương ứng số bit Host = 5 ➔ Số bit mạng Prefix = 32 - 5 = **`/27`**). Bước nhảy của dải này là 32 đơn vị.
* **Cấp phát tài nguyên:** Lấy đúng 32 đơn vị tính từ cột mốc số .64 gán cho phòng C (Điểm kết thúc dải là: 64 + 32 = 96):
  * ➔ **Subnet chính thức Phòng C: `192.168.1.64/27`**
  * *Bóc tách dải số:* Địa chỉ mạng là `.1.64`, địa chỉ quảng bá Broadcast là `.1.95`. Dải IP Host khả dụng gán cho máy tính nhân viên phòng C chạy liên tục từ **`192.168.1.65` đến `192.168.1.94`**.

#### 3. Khúc cắt 3: Quy hoạch cho Phòng D (15 Hosts - Ưu tiên số 3)
* **Lập luận tính toán:** Khối bánh còn thừa tiếp theo của công ty bắt đầu từ vị trí cột mốc số **`.96`**. Nhu cầu thực tế phòng D = 15 máy trạm + 2 địa chỉ chết = 17 IP. Khối mạng nhỏ nhất thỏa mãn lớn hơn hoặc bằng 17 là khối 2^5 = 32 địa chỉ (Tương ứng số bit Host = 5 ➔ Số bit mạng Prefix = 32 - 5 = **`/27`**). Bước nhảy của dải này là 32 đơn vị.
* **Cấp phát tài nguyên:** Lấy đúng 32 đơn vị tính từ cột mốc số .96 gán cho phòng D (Điểm kết thúc dải là: 96 + 32 = 128):
  * ➔ **Subnet chính thức Phòng D: `192.168.1.96/27`**
  * *Bóc tách dải số:* Địa chỉ mạng là `.1.96`, địa chỉ quảng bá Broadcast là `.1.127`. Dải IP Host khả dụng gán cho máy tính nhân viên phòng D chạy liên tục từ **`192.168.1.97` đến `192.168.1.126`**.

#### 4. Khúc cắt 4: Quy hoạch cho Phòng B (8 Hosts - Ưu tiên cuối cùng)
* **Lập luận tính toán:** Khối bánh còn thừa của công ty bắt đầu từ vị trí cột mốc số **`.128`**. Nhu cầu thực tế phòng B = 8 máy trạm + 2 địa chỉ chết = 10 IP. Khối mạng nhỏ nhất thỏa mãn lớn hơn hoặc bằng 10 là khối 2^4 = 16 địa chỉ (Tương ứng số bit Host = 4 ➔ Số bit mạng Prefix = 32 - 4 = **`/28`**). Bước nhảy của dải này là 16 đơn vị.
* **Cấp phát tài nguyên:** Lấy đúng 16 đơn vị tính từ cột mốc số .128 gán cho phòng B (Điểm kết thúc dải là: 128 + 16 = 144):
  * ➔ **Subnet chính thức Phòng B: `192.168.1.128/28`**
  * *Bóc tách dải số:* Địa chỉ mạng là `.1.128`, địa chỉ quảng bá Broadcast là `.1.143`. Dải IP Host khả dụng gán cho máy tính nhân viên phòng B chạy liên tục từ **`192.168.1.129` đến `192.168.1.142`**.

> 💾 **Quản trị kho tài nguyên dự trữ:** Toàn bộ kho không gian địa chỉ IP còn thừa dư dả tính từ cột mốc số **`.144` kéo dài cho đến kịch dải `.255`** của hệ thống /24 ban đầu sẽ được thiết bị trung tâm đóng băng, giữ lại làm kho dự trữ chiến lược. Sau này nếu công ty mở rộng thêm phòng ban mới, kỹ sư chỉ việc lôi kho dự trữ này ra cắt nhỏ tiếp.
