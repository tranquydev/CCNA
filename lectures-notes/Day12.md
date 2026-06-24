# Day 12: IPv6

## I. Chữa Bài tập thiết kế VLSM nâng cao bằng Phương pháp Bước Nhảy

### Kịch bản quy hoạch dải số phòng ban theo nhu cầu:
* **Sắp xếp thứ tự ưu tiên (Nhu cầu từ lớn đến bé):** Phòng 300 máy (Cần khối /23) ➔ Phòng 100 máy (Cần khối /25) ➔ Phòng 30 máy (Cần khối /27) ➔ Phòng 10 máy (Cần khối /28).

### Tiến trình nhẩm tịnh tiến bước nhảy liên hoàn:
* **Phòng 300 máy (Cần nhãn `/23` -> Bước nhảy tương ứng bằng 2 tại Octet thứ 3):** Lấy luôn cột mốc đầu tiên của dải mạng mẹ gốc 
  * ➔ **Subnet 1:** **`10.10.8.0/23`** (Chiếm trọn tài nguyên dải số từ .8.0 đến .9.255). Kho tài nguyên thừa kế tiếp của công ty dịch chuyển lên cột mốc: `8 + 2 = 10` (Tức mốc `10.10.10.0`).
* **Phòng 100 máy (Cần nhãn `/25` -> Bước nhảy tương ứng bằng 128 tại Octet thứ 4):** Xuất phát trực tiếp tại cột mốc số `.10.0` vừa nhận 
  * ➔ **Subnet 2:** **`10.10.10.0/25`** (Chiếm trọn tài nguyên dải số từ .10.0 đến .10.127). Điểm tài nguyên dư thừa kế tiếp tịnh tiến lên mốc: `0 + 128 = 128`.
* **Phòng 30 máy (Cần nhãn `/27` -> Bước nhảy tương ứng bằng 32 tại Octet thứ 4):** Xuất phát trực tiếp tại cột mốc số `.10.128` vừa nhận 
  * ➔ **Subnet 3:** **`10.10.10.128/27`** (Chiếm trọn tài nguyên dải số từ .10.128 đến .10.159). Điểm tài nguyên dư thừa kế tiếp tịnh tiến lên mốc: `128 + 32 = 160`.
* **Phòng 10 máy (Cần nhãn `/28` -> Bước nhảy tương ứng bằng 16 tại Octet thứ 4):** Xuất phát trực tiếp tại cột mốc số `.10.160` vừa nhận 
  * ➔ **Subnet 4:** **`10.10.10.160/28`** (Chiếm trọn tài nguyên dải số từ .10.160 đến .10.175).

---

## II. Bản chất cấu trúc phần cứng Địa chỉ IPv6 & Hệ số Hexadecimal

### 1. Lý do lịch sử dịch chuyển công nghệ
Không gian địa chỉ IPv4 (Cấu tạo từ 32 bit nhị phân) chỉ cung cấp tối đa xấp xỉ 4,3 tỷ địa chỉ IP. Sự bùng nổ mạnh mẽ của làn sóng thiết bị thông minh kết nối internet và hạ tầng IoT (Internet of Things) đã khiến quỹ địa chỉ IPv4 cạn kiệt hoàn toàn. 

Để giải quyết triệt để bài toán thiếu hụt này, tổ chức công nghệ quốc tế ban hành dải địa chỉ thế hệ mới **IPv6 có độ dài lên tới 128 bit nhị phân** (Sở hữu không gian khổng lồ vô tận 2^128 địa chỉ).

### 2. Hệ cơ số Hexadecimal (Thập lục phân - Hệ 16)
Vì chuỗi 128 bit nhị phân khi viết ra quá dài và dễ gây nhầm lẫn, địa chỉ IPv6 quy ước gom cụm và trình diễn dữ liệu sang hệ cơ số Hexadecimal bao gồm 16 ký tự định danh chạy từ số `0` đến chữ `F`.

* **Bảng quy chuẩn ký tự chữ cái:** Chữ `A` = 10, `B` = 11, `C` = 12, `D` = 13, `E` = 14, `F` = 15.

### 3. Cấu trúc phân rã hình khối của IPv6
* Chuỗi 128 bit nhị phân được chia đều thành **8 phân vùng bằng nhau**, mỗi phân vùng chiếm giữ đúng **16 bit**.
* Trong thuật ngữ chuyên ngành mạng mạng, mỗi phân vùng 16 bit này được gọi tên là một **Hextet**.
* 16 bit nhị phân của một Hextet sẽ tương ứng mã hóa rút gọn vừa vặn bằng **4 ký tự chữ số Hexa**. Giữa các khối Hextet được ngăn cách nhau bởi **Dấu hai chấm (:)**.

> 🎯 **Quy ước ranh giới cố định phần cứng:** 1 ký tự bit hệ Hexa = Đúng 4 bit nhị phân (Binary).

---

## III. Bộ ba Quy tắc viết rút gọn Địa chỉ IPv6 kịch khung

Để cắt giảm độ dài loằng ngoằng của dải chữ số IPv6 khi quản trị, kỹ sư áp dụng nghiêm ngặt bộ 3 quy tắc vàng sau:

### Quy tắc 1: Cắt tỉa các số 0 ngoài cùng bên trái khối Hextet
* Trong phạm vi cấu trúc của một khối Hextet, các số số 0 nằm ở vị trí tiên phong đứng đầu bên trái được phép xóa bỏ hoàn toàn. Các số 0 nằm ở giữa hoặc bên phải bắt buộc phải giữ nguyên để tránh làm sai lệch giá trị của khối.
  * *Ví dụ:* Khối `0DB8` rút gọn thành `DB8` | Khối `00A0` rút gọn thành `A0` | Khối `0001` rút gọn thành `1`.
  * *Lưu ý:* Khối `B500` bắt buộc giữ nguyên hoàn toàn, không được xóa vì hai số 0 nằm ở phía bên phải.
* *Trường hợp khối toàn số 0 (`0000`):* Được phép cắt tỉa xóa bỏ 3 ký tự số 0 đứng trước, bắt buộc phải giữ lại độc nhất một số `0` để làm nhãn đại diện cho khối đó.

### Quy tắc 2: Nén dải khối toàn số 0 bằng dấu đôi hai chấm (`::`)
* Khi trong địa chỉ xuất hiện một hoặc nhiều khối Hextet chứa toàn số 0 nằm liên tiếp sát cạnh nhau, kỹ sư được phép xóa sạch toàn bộ cụm khối đó và thay thế vắn tắt bằng nhãn duy nhất là dấu **`::`**.
  * *Ví dụ:* Dải gốc `2001:0DB8:0000:0000:0000:0000:0000:0001` được rút gọn kịch khung thành: **`2001:DB8::1`**

### Quy tắc 3: Luật cấm lạm dụng dấu đôi hai chấm
* 🚫 **ĐIỀU CẤM KỴ:** Trong cấu trúc của một địa chỉ IPv6 duy nhất, nhãn dấu đôi hai chấm (`::`) **chỉ được phép xuất hiện độc nhất 1 lần duy nhất**.
* *Lý do phần cứng:* Nếu một địa chỉ chứa đồng thời 2 dấu `::` (Ví dụ dải số lỗi: `1998:A06::4E::5`), hệ điều hành máy tính khi dịch ngược lại sang mã nhị phân sẽ bị dính lỗi mất phương hướng toán học. Thiết bị không thể tự xác định dấu `::` thứ nhất đang che giấu bao nhiêu khối số 0 và dấu `::` thứ hai đang che giấu bao nhiêu khối số 0, dẫn đến tiến trình khôi phục gói tin thất bại hoàn toàn.
* *Giải pháp xử lý:* Nếu địa chỉ có 2 cụm khối 0 nằm rời rạc nhau, kỹ sư ưu tiên nén `::` cho cụm khối 0 có độ dài lớn nhất (để tiết kiệm chữ số nhất). Cụm khối 0 còn lại bắt buộc phải giữ lại một số 0 lẻ đại diện theo đúng Quy tắc 1.

---

## IV. Toán mạng nâng cao: Kỹ thuật mượn Bit chia Subnet IPv6

* **Bản chất luật chơi:** Chia mạng con IPv6 bắt buộc phải sử dụng phương pháp mượn bit nhị phân truyền thống từ vùng Host Part. Kỹ sư hoàn toàn không sử dụng được phương pháp Bước Nhảy thập phân do hệ số chữ cái Hexa không thể cộng tịnh tiến đều thông thường.

### 🛠️ Bài toán mẫu: 
Cho dải mạng mẹ IPv6 gốc ban đầu là `2001:DB8:A::/48`. Hãy thực hiện bẻ nhỏ dải này thành các khối mạng con có thông số nhãn Prefix mới là `/50`.

* **Bước 1 (Tính số khối mạng tạo thành):** Từ độ dài /48 nâng cấp lên /50 ta vay mượn thêm: `50 - 48 = 2` bit nhị phân. Số lượng khối mạng con sinh ra tương ứng bằng: 2^2 = **4 khối mạng**.
* **Bước 2 (Phân rã Hextet chứa điểm cắt bit):** Ba khối Hextet đầu (`2001:DB8:A`) chiếm đúng 48 bit Network Part đầu tiên nên được khóa cứng giữ nguyên. Điểm mượn bit nằm ở khối Hextet thứ 4. Ta tiến hành phân rã 4 ký tự Hexa của Hextet thứ 4 sang 16 bit nhị phân, bốc đúng 2 bit đầu tiên làm bit mượn nhảy qua 4 trạng thái (`00`, `01`, `10`, `11`), toàn bộ các bit Host còn lại gán bằng số 0:
  * **Khối con 1 (Trường hợp mượn cụm bit 00):**  Mã nhị phân Hextet 4: `[00]00.0000.0000.0000` (Binary) ➔ Quy đổi hệ Hexa bằng: `0000`.
    * Kết quả nén lại: **`2001:DB8:A::/50`**
  * **Khối con 2 (Trường hợp mượn cụm bit 01):**  Mã nhị phân Hextet 4: `[01]00.0000.0000.0000` (Binary) ➔ Quy đổi hệ Hexa bằng: `4000`. (Vị trí bit thứ hai mang số 1 tương ứng giá trị 2^2 = 4).
    * Kết quả nén lại: **`2001:DB8:A:4000::/50`**
  * **Khối con 3 (Trường hợp mượn cụm bit 10):**  Mã nhị phân Hextet 4: `[10]00.0000.0000.0000` (Binary) ➔ Quy đổi hệ Hexa bằng: `8000`. (Vị trí bit thứ ba mang số 1 tương ứng giá trị 2^3 = 8).
    * Kết quả nén lại: **`2001:DB8:A:8000::/50`**
  * **Khối con 4 (Trường hợp mượn cụm bit 11):**  Mã nhị phân Hextet 4: `[11]00.0000.0000.0000` (Binary) ➔ Quy đổi hệ Hexa bằng: `C000`. (Tổ hợp bit 1100 tương ứng 2^3 + 2^2 = 8 + 4 = 12, theo quy ước mã chữ cái hệ 16 là chữ C).
    * Kết quả nén lại: **`2001:DB8:A:C000::/50`**

---

## V. Thực hành tra cứu đề thi Chứng chỉ Quốc tế & Bài tập về nhà

Câu hỏi tính toán địa chỉ IPv6 trích từ Kho đề thi Chứng chỉ Quốc tế Cisco CCNA

* **Đề bài thi mẫu:** Cho Subnet `2001:DB8:3C55:8D2::/64`. Hãy xác định địa chỉ IP Host khả dụng đầu tiên và địa chỉ IP Host khả dụng cuối cùng nằm trong dải mạng con này?
* **Đáp án giải nhẩm siêu tốc :**
  * *IP Host khả dụng đầu tiên:* Được xác định bằng cách cộng thêm 1 đơn vị vào bit cuối cùng của dải số mạng ➔ **`2001:DB8:3C55:8D2::1`**
  * *IP Host khả dụng cuối cùng:* Được xác định bằng cách gán tất cả các bit thuộc vùng Host Part bằng số 1, ngoại trừ bit khóa cửa cuối cùng gán bằng số 0 ➔ **`2001:DB8:3C55:8D2:FFFF:FFFF:FFFF:FFFE`**

### 🎯 Nhiệm vụ về nhà bắt buộc Day 12:
* Viết lại toàn bộ tiến trình lập luận bóc tách nhị phân phân rã tìm 4 khối mạng con của dải `/50`.
