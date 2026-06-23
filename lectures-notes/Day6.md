# Day 6: OSI P2 & Toán Học Nhị Phân (Binary)

## I. Ba phương thức Truyền thông mạng & Ôn tập 7 Layer OSI

### 1. Ba phương thức truyền thông căn bản
* **Unicast (1 - 1):** Truyền thông đích xác từ một nguồn phát tới một thiết bị nhận duy nhất.
* **Multicast (1 - Nhóm):** Truyền dữ liệu tới một nhóm thiết bị đích cụ thể trong mạng (không phát cho tất cả).
* **Broadcast (1 - Tất cả):** Phóng gói tin đại diện cho tất cả thành viên trong mạng LAN (Địa chỉ MAC đích mặc định: `FFFF.FFFF.FFFF`). Khi nhận gói tin này, Switch sẽ chạy lệnh `Flooding` để nhân bản và phát ra mọi cổng (trừ cổng nhận vào).

### 2. Ranh giới công việc kỹ sư trong mô hình OSI
* **Nhóm tầng Ứng dụng (Layer 5, 6, 7):** Bao gồm *Application, Presentation, Session*. Đây là vùng làm việc của lập trình viên phần mềm (Developer), chịu trách nhiệm thiết lập và tạo ra Dữ liệu thô (**Data**).
* **Nhóm tầng Hạ tầng (Layer 2, 3, 4):** Bao gồm *Data Link, Network, Transport*. Đây là phân vùng quản trị nòng cốt của kỹ sư mạng, chịu trách nhiệm xử lý luồng đi, định tuyến và tìm đường đi tối ưu cho gói tin xuyên biên giới.

---

## II. Đơn vị Dữ liệu (PDU) & Quy trình bóc vỏ gói tin (De-encapsulation)

### 1. Tên gọi đơn vị dữ liệu (PDU) qua từng Layer từ trên xuống
Khi máy phát chạy tiến trình **Data Encapsulation** (Đóng gói dữ liệu) từ tầng 7 xuống tầng 1, qua mỗi Layer gói tin sẽ được khoác thêm một lớp phong bì tiêu đề (Header) và đổi tên gọi tương ứng:

* `Layer 7, 6, 5` $\rightarrow$ **Data** (Dữ liệu thô).
* `Layer 4` $\rightarrow$ Bọc thêm *Layer 4 Header* (nhãn TCP/UDP) $\rightarrow$ Đổi tên thành **Segment** (Phân đoạn).
* `Layer 3` $\rightarrow$ Bọc thêm *Layer 3 Header* (nhãn chứa IP Source / IP Dest) $\rightarrow$ Đổi tên thành **Packet** (Gói tin).
* `Layer 2` $\rightarrow$ Bọc thêm *Layer 2 Header* (nhãn chứa MAC Source / MAC Dest) và đuôi kiểm lỗi *Frame Trailer* $\rightarrow$ Đổi tên thành **Frame** (Khung dữ liệu).
* `Layer 1` $\rightarrow$ Phân rã Frame thành dải xung điện/ánh sáng $\rightarrow$ Gọi là **Bits** (Các bit nhị phân 0 và 1) phát lên dây cáp vật lý.

### 2. Quy trình bóc vỏ tại Máy nhận - De-encapsulation
Ngược lại với máy phát, thiết bị nhận (Destination) khi đón các bit nhị phân lọt vào cạc mạng tại Layer 1 sẽ thực hiện tiến trình **De-encapsulation** (Bóc vỏ gói tin) tuần tự ngược từ tầng dưới lên tầng trên.



> 🎯 **Thuật toán Double-Check tối ưu tài nguyên:**
> Tại Layer 2, máy nhận bóc lớp vỏ Frame để đọc trường *MAC Destination*. Nếu trùng khớp địa chỉ MAC của chính nó hoặc là mã *Broadcast*, nó mới đồng ý bóc tiếp lớp vỏ tiếp theo để đưa lên Layer 3 đọc nhãn IP. 
> 
> Nếu ngay từ tầng 2 đọc thấy MAC đích gán cho một máy khác, thiết bị sẽ thực hiện lệnh **Drop** cưỡng bức hủy bỏ gói tin ngay lập tức. Hành động này giúp Router/Switch bảo vệ bộ nhớ RAM, không tốn tài nguyên CPU để xử lý sâu vào bên trong những gói tin rác.

---

## III. Thuật toán chuyển đổi hệ Nhị phân (Binary) sang Thập phân (Decimal)

Để chuẩn bị cho chương chia mạng con IPv4, học viên bắt buộc phải nhẩm tay thành thạo quy tắc chuyển đổi từ hệ cơ số 2 sang hệ cơ số 10.

### 📐 Quy tắc 2 bước tính giá trị Thập phân của dải bit:
* **Bước 1 (Đánh số số thứ tự bit):** Đếm dải số nhị phân theo ranh giới từ **phải qua trái** và bắt buộc bắt đầu đếm số thứ tự từ số **0** (Hệ thống máy tính luôn chạy từ số 0).
* **Bước 2 (Tính tổng lũy thừa):** Áp dụng công thức khai triển:
$$\mathbf{\text{Giá trị Thập phân (Decimal)}} = \sum \left( \mathbf{\text{Giá trị của bit (0 hoặc 1)}} \times \mathbf{2^{\text{Số thứ tự của bit}}} \right)$$

### 🛠️ Ví dụ minh họa: Tính dải bit `1001101`
* **Bước 1:** Đánh số thứ tự bit từ phải sang trái:

| 1 | 0 | 0 | 1 | 1 | 0 | 1 |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| bit 6 | bit 5 | bit 4 | bit 3 | bit 2 | bit 1 | bit 0 |

* **Bước 2:** Triển khai phép tính lũy thừa:
$$\text{Decimal} = (1 \times 2^6) + (0 \times 2^5) + (0 \times 2^4) + (1 \times 2^3) + (1 \times 2^2) + (0 \times 2^1) + (1 \times 2^0)$$
$$\text{Decimal} = 64 + 0 + 0 + 8 + 4 + 0 + 1 \quad \text{(Quy ước } 2^0 = 1\text{)}$$
$$\mathbf{\text{Decimal}} = \mathbf{77}$$

---

## IV. Thuật toán chuyển đổi hệ Thập phân sang Nhị phân

* **Cơ chế chia dư liên tục:** Để đổi một số thập phân sang hệ nhị phân, ta lấy số đó **chia liên tục cho 2**, ghi nhận lại số dư (`0` hoặc `1`). Tiến trình chia kết thúc khi nào phần thương số đạt bằng số **0**.
* **Cách viết đáp án:** Thu gom toàn bộ các số dư của các phép chia và **viết ngược thứ tự từ dưới lên trên** để ra chuỗi bit Binary chính xác.

### 🛠️ Ví dụ minh họa: Chuyển đổi số thập phân `50` sang Nhị phân
* $50 / 2 = 25 \rightarrow$ dư **0** *(bắt đầu)*
* $25 / 2 = 12 \rightarrow$ dư **1**
* $12 / 2 = 6 \rightarrow$ dư **0**
* $6 / 2 = 3 \rightarrow$ dư **0**
* $3 / 2 = 1 \rightarrow$ dư **1**
* $1 / 2 = 0 \rightarrow$ dư **1** *(Thương số bằng 0 $\rightarrow$ Dừng tiến trình)*

➔ Thu gom số dư ngược từ dưới lên $\rightarrow$ Chuỗi nhị phân của số 50 là: **`110010`**

---

## V. Bảng Hằng số Lũy thừa cơ số 2 bắt buộc thuộc lòng

Dải hằng số lũy thừa của 2 từ mốc 0 đến mốc 8 là phản xạ ép buộc phải thuộc lòng để tính toán Subnet Mask và chia dải IP siêu tốc (không dùng máy tính):

| Phép tính lũy thừa | Giá trị Thập phân | Ý nghĩa ứng dụng dải bit trong Subnet Mask / IP |
| :-: | :-: | :--- |
| $2^0$ | **1** | Giá trị của bit cuối cùng trong dải số Octet |
| $2^1$ | **2** | Bước nhảy dải mạng con tối thiểu |
| $2^2$ | **4** | Phân mạch dải mạng nhỏ cho kết nối WAN |
| $2^3$ | **8** | Dải mạng con phân hoạch thành 8 khối hệ thống |
| $2^4$ | **16** | Bước nhảy mạng con thông dụng cho các phòng ban |
| $2^5$ | **32** | Khối IP tối ưu cho dải phòng có 30 thiết bị |
| $2^6$ | **64** | Khối IP tối ưu cho dải phòng có 60 thiết bị |
| $2^7$ | **128** | Ranh giới chia đôi dải IP của một mạng lớn |
| $2^8$ | **256** | Giá trị giới hạn kịch trần số lượng của 1 Octet IPv4 |

### ❌ Quy tắc kỷ luật bắt buộc:
Toàn bộ quá trình làm toán mạng tuyệt đối không được đụng vào máy tính cầm tay Casio hay ứng dụng Calculator trên điện thoại. Tất cả các phép tính hoán đổi bắt buộc phải nhẩm miệng hoặc đặt bút tính nháp ra vở để kích hoạt tư duy phản xạ nhạy bén với các con số.

---

## VI. Kết quả & Bài tập về nhà
* **Đã hoàn thành hoán đổi nhị phân tại lớp (Correct 100%):**
  * Thập phân $\rightarrow$ Nhị phân: $125 \rightarrow \mathbf{1111101} \mid 200 \rightarrow \mathbf{11001000} \mid 160 \rightarrow \mathbf{10100000}$
  * Nhị phân $\rightarrow$ Thập phân: $1111001 \rightarrow \mathbf{121} \mid 1010110 \rightarrow \mathbf{86} \mid 10001110 \rightarrow \mathbf{142}$
* **Nhiệm vụ :** Học thuộc lòng bảng hằng số lũy thừa cơ số 2 từ $2^0$ đến $2^8$. 
