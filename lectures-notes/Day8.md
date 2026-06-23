# Day 8: IP P2

## I. Sửa bài tập về nhà Day 7 - Thuật toán kiểm tra tính hợp lệ của Subnet

**Bản chất bài toán:** Xác định xem trong 3 biểu thức sau, biểu thức nào đạt chuẩn cấu trúc của một Subnet mạng con chính quy. Điều kiện bắt buộc là địa chỉ đứng trước dấu xuộc phải là địa chỉ **Network ID** (IP đầu dải, có toàn bộ các bit phần Host Part gán bằng số 0).
* **A.** `192.168.1.5/28`
* **B.** `192.168.1.16/29`
* **C.** `10.10.5.192/26`

### 🛠️ Tiến trình bóc tách giải mã nhị phân chi tiết:

* **Xét ý A (`192.168.1.5/28`):** Prefix $= 28 \rightarrow$ Khóa cứng 28 bit đầu làm Network Part, còn lại $32 - 28 = 4$ bit cuối là Host Part. Khai triển Octet thứ 4 (số 5) sang 8 bit nhị phân:
$$\text{Số 5} = \mathbf{0000}.0101 \rightarrow \text{Ghép nhãn Prefix 28 bit: } \underbrace{192.168.1.\mathbf{0000}}_{28 \text{ bit Net Part}} \ \underbrace{0101}_{4 \text{ bit Host Part}}$$
  * ➔ *Đánh giá:* 4 bit phần Host đang mang giá trị `0101` (khác trạng thái full 0). Do không phải IP bé nhất đầu dải, số `.5` chỉ là một địa chỉ IP Host thông thường. Biểu thức A **Sai kỹ thuật** - Không phải một Subnet.

* **Xét ý B (`192.168.1.16/29`):** Prefix $= 29 \rightarrow$ Khóa cứng 29 bit Net Part, còn lại $32 - 29 = 3$ bit cuối làm Host Part. Khai triển Octet thứ 4 (số 16):
$$\text{Số 16} = \mathbf{0010.0}000 \rightarrow \text{Ghép nhãn Prefix 29 bit: } \underbrace{192.168.1.\mathbf{0001.0}}_{29 \text{ bit Net Part}} \ \underbrace{000}_{3 \text{ bit Host Part}}$$
  * ➔ *Đánh giá:* Toàn bộ 3 bit phần Host đều bằng số 0 (trạng thái full 0). Đây chính là địa chỉ Network ID đầu dải số. Biểu thức B **Hợp lệ** - Là một Subnet chính quy.

* **Xét ý C (`10.10.5.192/26`):** Prefix $= 26 \rightarrow$ Khóa cứng 26 bit Net Part, còn lại $32 - 26 = 6$ bit cuối làm Host Part. Khai triển Octet thứ 4 (số 192):
$$\text{Số 192} = \mathbf{11}00.0000 \rightarrow \text{Ghép nhãn Prefix 26 bit: } \underbrace{10.10.5.\mathbf{11}}_{26 \text{ bit Net Part}} \ \underbrace{00.0000}_{6 \text{ bit Host Part}}$$
  * ➔ *Đánh giá:* Toàn bộ 6 bit phần Host đều đạt trạng thái full 0 kịch khung. Biểu thức C **Hợp lệ** - Là một Subnet chính quy.

> ❌ **Lỗi trình bày nghiêm trọng cần tránh:**
> Khi viết đáp án cho một mạng con, bắt buộc phải ghi đầy đủ cấu trúc gồm 3 thành phần (Ví dụ: `192.168.1.16/29`). Tuyệt đối không được viết tắt lược bỏ dấu xuộc thành số thô cô độc `192.168.1.16`. Số thô không chứa Prefix chỉ được hiểu là một địa chỉ IP cá nhân của máy trạm Client, hoàn toàn không đại diện cho mạng con Subnet.

---

## II. Chuyên đề Toán Mạng 1: Bóc tách các thông số của một Subnet cho trước

**Đề bài toán mẫu:** Cho Subnet `192.168.2.0/23`. Hãy tính tổng lượng IP, tìm địa chỉ Network ID, Broadcast IP, và xác định dải IP Host cấp cho máy trạm?

### 🛠️ Tiến trình xử lý:
1. **Tính tổng số lượng địa chỉ IP:** Prefix $= 23 \rightarrow$ Số bit phần Host Part $= 32 - 23 = 9$ bit. Áp dụng công thức số trường hợp cấu thành, tổng số lượng IP nằm trong mạng con này là: $2^9 = \mathbf{512}$ địa chỉ IP.
2. **Tìm địa chỉ Network ID (IP Đầu dải số - Host bit = full 0):** Khai triển Octet thứ 3 (số 2) sang nhị phân: $\text{Số 2} = \mathbf{0000.001}0$. Ghép nhãn khóa cứng 23 bit Net Part, gán 9 bit phần Host bằng số 0:
$$\underbrace{192.168.\mathbf{0000.001}}_{23 \text{ bit Net Part khóa cứng}} \ \mathbf{0} \cdot \underbrace{0000.0000}_{8 \text{ bit Octet 4}}$$
  * Gộp 8 bit nhị phân về lại dạng thập phân từng Octet ➔ **Network ID: `192.168.2.0`**.
3. **Tìm địa chỉ Broadcast IP (IP Cuối dải số - Host bit = full 1):** Giữ nguyên 23 bit vùng Net Part, gán kịch trần 9 bit phần Host bằng số 1:
$$\underbrace{192.168.\mathbf{0000.001}}_{23 \text{ bit Net Part khóa cứng}} \ \mathbf{1} \cdot \underbrace{1111.1111}_{8 \text{ bit Octet 4}}$$
  * Tính toán giá trị thập phân từng vùng:
    * Octet thứ 3: Cụm bit $\mathbf{0000.001}1 \rightarrow$ Khai triển thành: $2^1 + 2^0 = 2 + 1 = \mathbf{3}$.
    * Octet thứ 4: Cụm 8 bit 1 kịch trần $\mathbf{1111.1111} \rightarrow$ Đạt giá trị tối đa là `255`.
  * ➔ **Broadcast IP: `192.168.3.255`**.
4. **Xác định dải IP Host khả dụng cấp cho máy trạm:** Nằm ở khúc giữa dải số (liền sau Network ID và liền trước Broadcast IP) ➔ **Dải IP Host: Từ `192.168.2.1` đến `192.168.3.254`**.

> ⚠️ **Cảnh báo lỗi bẫy tư duy hạ tầng:**
> Đối với các dải mạng lớn có Prefix ngắn như `/23`, `/22`, `/21`... trở xuống, hai địa chỉ `192.168.2.255` và `192.168.3.0` hoàn toàn là địa chỉ **IP Host khả dụng chính quy**. Kỹ sư có đầy đủ quyền gán trực tiếp chúng lên cạc mạng của PC/Router để ra internet bình thường. Ranh giới bị cấm chỉ độc nhất nằm ở đầu dải thực tế (`.2.0`) và cuối dải thực tế (`.3.255`).

---

## III. Chuyên đề Toán Mạng 2: Truy tìm Subnet gốc của một địa chỉ IP Host

**Đề bài toán mẫu:** Cho địa chỉ IP Host `10.10.10.40/22`. Hãy xác định xem máy tính này đang thuộc về dải mạng con Subnet nào?

### 🛠️ Tiến trình xử lý:
* **Bước 1:** Prefix $= 22 \rightarrow$ Khóa cứng 22 bit làm vùng mạng. Ta phân rã Octet thứ 3 (số 10) sang 8 bit nhị phân để dò lanh giới cắt bit: $\text{Số 10} = \mathbf{0000.10}10$.
* **Bước 2:** Ranh giới cắt bit 22 nằm khía tại vị trí bit số 2 của Octet thứ 3. Tiến hành khóa cứng hoàn toàn 22 bit đầu, chạy thuật toán tìm IP đầu dải bằng cách ép toàn bộ 10 bit phần Host còn lại quay về số 0:
$$\underbrace{10 \cdot 10 \cdot \mathbf{0000.10}}_{22 \text{ bit Net Part khóa cứng}} \ \mathbf{00} \cdot \underbrace{0000.0000}_{8 \text{ bit Octet 4}}$$
* **Bước 3:** Biên dịch ngược lại hệ thập phân từng Octet:
  * Octet thứ 3: Cụm bit $\mathbf{0000.10}00 \rightarrow$ Vị trí bit số 3 ($2^3$) mang số 1 ➔ Đạt giá trị thập phân là **`8`**.
  * Octet thứ 4: Toàn bộ 8 bit 0 ➔ Đạt giá trị là **`0`**.

➔ **Đáp án Subnet gốc của thiết bị là: `10.10.8.0/22`**.

---

## IV. Chuyên đề Toán Mạng 3: Bài toán Thiết kế Quy hoạch Mạng Doanh nghiệp



**Đề bài toán thiết kế:** Văn phòng công ty có quy mô gồm chính xác **15 người dùng** (tương ứng 15 thiết bị máy tính PC cần cấp IP). Bạn hãy tính toán thiết kế và cấp một dải Subnet tối ưu sát sườn nhất cho văn phòng này để tiết kiệm chi phí mua quỹ IP công cộng từ nhà mạng ISP.

### 🛠️ Tiến trình lập luận và thiết kế của Kỹ sư:
* **Bước 1 (Tính tổng nhu cầu):** Ngoài 15 địa chỉ gán cho máy tính nhân viên, hệ thống luôn luôn phải trích thêm 2 địa chỉ chết cố định bắt buộc (gồm 1 IP làm địa chỉ mạng Network ID và 1 IP làm địa chỉ quảng bá Broadcast IP).
$$\rightarrow \text{Tổng lượng IP tối thiểu hệ thống cần tiêu tốn} = 15 + 2 = \mathbf{17 \text{ địa chỉ IP}}$$
* **Bước 2 (Áp dụng khối lũy thừa cơ số 2):**
  * *Nếu chọn dải bit Host Part $= 4$:* Khối IP sở hữu là $2^4 = 16$ địa chỉ $\rightarrow$ Trừ 2 địa chỉ chết còn lại 14 IP ➔ **Không đủ** cấp cho 15 nhân sự.
  * *Nếu chọn dải bit Host Part $= 5$:* Khối IP sở hữu là $2^5 = 32$ địa chỉ $\rightarrow$ Trừ 2 địa chỉ chết còn lại 30 IP khả dụng ➔ **Thỏa mãn** chứa được dải 15 máy tính nhân viên và dư thừa chỗ trống cho mở rộng phòng ban sau này.
* **Bước 3 (Tính toán thông số Prefix):**
$$\text{Prefix} = 32 - \text{Số bit Host Part} \longrightarrow \text{Prefix} = 32 - 5 = \mathbf{27}$$

➔ **Đáp án dải quy hoạch thiết kế tối ưu nhất cho công ty:** Mọi dải mạng con có nhãn **/27** đứng sau (Ví dụ dải số: **`192.168.1.0/27`** hoặc **`10.10.10.0/27`**).

---

## V. Bài tập về nhà
* Công ty A có 15 người trong công ty -> Cấp cho A 1 subnet
