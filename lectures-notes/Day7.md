# Day 7: IP P1

## I. Thuật toán Nhẩm siêu tốc chuyển Thập phân sang Nhị phân (Cách Tách Khối)

### 1. Bản chất của phương pháp Tách Khối
Phương pháp chia cho 2 lấy số dư (Day 6) rất tốn thời gian và giấy nháp khi gặp các số thập phân lớn. Phương pháp **Tách Khối** bẻ gãy một số thập phân thành tổng của các hằng số lũy thừa cơ số 2 (ưu tiên chọn số lũy thừa lớn nhất nhưng phải nhỏ hơn và gần nhất với số thập phân đó).

### 2. Thuật toán nạp bit (Từ bit 7 xuống bit 0)
Căn cứ theo dải hằng số lũy thừa của 2. Vị trí lũy thừa nào xuất hiện trong phép tính tổng thì gán bit đó bằng `1`, vị trí nào khuyết không xuất hiện thì gán bit đó bằng `0`.

### 🛠️ Ví dụ minh họa: Chuyển đổi số `100` sang Nhị phân
* **Bước 1:** Tách khối lũy thừa cơ số 2 gần nhất ($2^6 = 64$):
$$100 = 64 + 32 + 4 \rightarrow 100 = 2^6 + 2^5 + 2^2$$
* **Bước 2:** Nạp giá trị bit theo số thứ tự vị trí Octet (8 bit):

| Giá trị lũy thừa | $2^7$ (128) | $2^6$ (64) | $2^5$ (32) | $2^4$ (16) | $2^3$ (8) | $2^2$ (4) | $2^1$ (2) | $2^0$ (1) |
| :--- | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| **Trạng thái nạp** | Khuyết | Có | Có | Khuyết | Khuyết | Có | Khuyết | Khuyết |
| **Giá trị Mã Bit** | **0** | **1** | **1** | **0** | **0** | **1** | **0** | **0** |

➔ Kết quả chuỗi bit nhị phân của số 100 là: **`1100100`** (bỏ số 0 vô nghĩa ở đầu).

---

## II. Bản chất cấu trúc phần cứng của Địa chỉ IPv4

* **Không gian bit:** Địa chỉ IPv4 được cấu tạo từ **32 bit** nhị phân chạy ngầm trong bo mạch phần cứng. 32 bit này được chia đều thành 4 phân vùng bằng nhau.
* **Thuật ngữ Octet:** Trong chuyên ngành mạng, mỗi phân vùng 8 bit này được gọi tên là một **Octet**. Giữa 4 Octet được ngăn cách nhau bởi một **Dấu chấm (.)**.



* **Giới hạn dải số của một Octet:** Vì một Octet chứa tối đa 8 bit nhị phân ($2^8 = 256$ trường hợp giá trị xảy ra):
  * *Giá trị thấp nhất* (khi cả 8 bit đều là số 0): Quy đổi sang thập phân là **`0`**.
  * *Giá trị cao nhất* (khi cả 8 bit đều gán số 1 - `11111111`): Quy đổi sang thập phân đạt tối đa là **`255`**.
* **⚠️ Quy tắc chặn lỗi địa chỉ:** Mọi Octet trong địa chỉ IPv4 bắt buộc phải nằm trong ranh giới từ **0 đến 255**. Nếu xuất hiện số vượt khung (Ví dụ: `192.260.100.5`), địa chỉ đó sai kỹ thuật hoàn toàn và không tồn tại trong thực tế.
* **Tổng lượng địa chỉ toàn cầu:** Không gian IPv4 sở hữu tổng cộng $2^{32} = 4.294.967.296$ địa chỉ (Xấp xỉ 4,3 tỷ IP). Quỹ địa chỉ này hiện đã cạn kiệt, buộc hạ tầng toàn cầu phải chuyển dịch sang dải **IPv6 gồm 128 bit** ($2^{128}$ địa chỉ) trong tương lai gần.

---

## III. Giải nghĩa Chuyên sâu Ma trận 9 Thuật ngữ Mạng con (Subnet)



### 1. Subnet (Mạng con)
Là một nhóm/tập hợp các địa chỉ IP có chung một phần mạng (**Network Part**) giống hệt nhau (tương tự như những người trong cùng một chi họ, bắt buộc phải mang chung một chữ Họ đứng đầu).

### 2. Cấu trúc bắt buộc của một Subnet
Một dải Subnet chính quy bắt buộc phải chứa đủ 3 thành phần liên kết theo cú pháp:
$$\mathbf{\text{Cú pháp chuẩn Subnet: }} \mathbf{\text{Network ID}} + \mathbf{\text{ Dấu xuộc ( / ) }} + \mathbf{\text{Prefix}}$$

### 3. Prefix (Độ dài dải mạng)
Là chữ số đứng ngay sau dấu gạch chéo xuộc (`/`), có nhiệm vụ chỉ định chính xác số lượng bit thuộc vùng Network (vùng giữ cố định khóa chết, không được phép thay đổi) tính từ trái qua phải của dải 32 bit IP.
* *Ví dụ:* Dải mạng `/24` chỉ định hệ thống khóa cứng đúng 24 bit đầu tiên làm dải gốc nội bộ.

### 4. Network Part (Vùng cố định) & Host Part (Vùng tự do)
* **Network Part:** Là dải bit bị khóa cứng làm nhãn đại diện cho cả một phòng ban mạng mạng.
* **Host Part:** Là dải bit còn lại trong tổng số 32 bit ($\text{Host bit} = 32 - \text{Prefix}$). Vùng bit này được phép thay đổi nhảy số tự do từ 0 sang 1 để tự gán tạo ra hàng loạt địa chỉ IP cấp cho máy trạm (Client).

### 5. Network ID (Địa chỉ mạng - IP Đầu dải)
Là địa chỉ đầu tiên, nhỏ nhất nằm trong một Subnet. 
* *Nhận diện vật lý:* Toàn bộ tất cả các bit thuộc phần **Host Part đều gán bằng số 0**.
* *Công năng:* Đóng vai trò làm mã số định danh đại diện cho cả một vùng mạng. **Cấm** không được gán địa chỉ này cho bất kỳ card mạng của thiết bị nào.

### 6. Broadcast IP (Địa chỉ quảng bá - IP Cuối dải)
Là địa chỉ cuối cùng, lớn nhất nằm trong một Subnet.
* *Nhận diện vật lý:* Toàn bộ tất cả các bit thuộc phần **Host Part đều gán bằng số 1**.
* *Công năng:* Làm địa chỉ đại diện phát loa thông báo đồng loạt trong mạng LAN. **Cấm** không được đặt IP này cho máy trạm.

### 7. Dải IP Host khả dụng (IP Host Range)
Là toàn bộ các địa chỉ IP nằm ở khúc giữa dải số mạng con (ngắt bỏ IP đầu dải `Network ID` và IP cuối dải `Broadcast IP`). Đây là dải tài nguyên duy nhất có quyền gán trực tiếp lên cạc mạng của PC, Router, Server.
$$\mathbf{\text{Công thức tính lượng IP cấp cho máy trạm: }} \mathbf{2^{\text{Số bit Host Part}} - 2}$$

---

## IV. Thử thách Tư duy logic 

**Đề bài:** Xác định xem trong 3 biểu thức sau, đâu là một dải Subnet mạng con chính quy hợp lệ (tức địa chỉ đứng trước dấu xuộc phải đạt chuẩn Network ID đầu dải - toàn bộ các bit Host Part phải bằng 0):
* **A.** `192.168.1.5/28`
* **B.** `192.168.1.16/29`
* **C.** `10.10.5.192/26`

---

### 💡 Lợi ý nhỏ hỗ trợ giải bài tập tư duy :

Để biết câu **A, B, hay C** là Subnet hợp lệ, bạn hãy nhìn vào số **Prefix** để xác định vùng bit Host Part, sau đó đổi số cuối cùng sang Nhị phân theo cách Tách khối của Day 7:

* **Câu A (`/28`):** Có $32 - 28 = 4$ bit Host ở cuối. Số cuối cùng là `5` $\rightarrow$ Nhị phân là `0000 0101`. Bạn nhìn xem 4 bit cuối cùng của nó có bằng 0 hết không? Nếu không bằng 0 thì đây là địa chỉ IP Host chứ không phải Network ID $\rightarrow$ Loại.
* **Câu B (`/29`):** Có $32 - 29 = 3$ bit Host ở cuối. Số cuối cùng là `16` $\rightarrow$ Nhị phân 8 bit là `0001 0000`. Bạn kiểm tra xem 3 bit cuối cùng của nó có phải là `000` không nhé!
* **Câu C (`/26`):** Có $32 - 26 = 6$ bit Host ở cuối. Số cuối cùng là `192` $\rightarrow$ Nhị phân 8 bit là `1100 0000`. Bạn đếm thử xem 6 bit cuối cùng của dải này có đạt chuẩn `000000` không nha!

