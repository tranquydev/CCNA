
# Day 2: OS Basic Config P1

## I. Thành phần của Hệ điều hành Cisco IOS
Tương tự như mọi hệ điều hành khác, Cisco IOS được cấu thành từ 3 phần chính:
* **Hardware (Phần cứng):** Bo mạch, chip, RAM... là những linh kiện vật lý.
* **Kernel (Nhân):** Phần lõi code giúp biên dịch các câu lệnh, đóng vai trò giao tiếp và chuyển hóa phần mềm (Software) để điều khiển phần cứng (Hardware).
* **Shell (Giao diện người dùng):** Giao diện để con người cấu hình và thao tác với thiết bị. Có hai loại giao diện Shell chính:

| Loại Giao Diện | Đặc Điểm | Ưu / Nhược Điểm |
| :--- | :--- | :--- |
| **GUI**<br>*(Graphic User Interface)* | Sử dụng chuột, icon, hình ảnh (Ví dụ: Windows, giao diện cấu hình Modem...). | **Ưu:** Thân thiện, dễ dùng.<br>**Nhược:** Bị giới hạn, không cấu hình được tính năng chuyên sâu. |
| **CLI**<br>*(Command Line Interface)* | Sử dụng các câu lệnh dạng văn bản (text) (Ví dụ: CMD trên Windows, Terminal trên Cisco). | **Ưu:** Rất linh hoạt, can thiệp sâu vào mọi cấu hình đặc thù.<br>**Nhược:** Khó dùng, yêu cầu phải học bài bản. |

---

## II. Phương thức truy cập thiết bị mạng
Để truy cập vào giao diện CLI của thiết bị mạng Cisco, kỹ sư có 2 cách chính:

### 1. Truy cập trực tiếp (Console)
* **Cách làm:** Sử dụng cáp Console vật lý (Một đầu USB cắm vào máy tính, một đầu RJ45 cắm vào cổng Console trên Router/Switch).
* **Ưu điểm:** Dễ thực hiện, không cần thiết bị phải có cấu hình mạng hay IP từ trước.
* **Nhược điểm:** Giới hạn khoảng cách (phải đứng gần thiết bị), thiếu tính linh hoạt khi xử lý sự cố từ xa.

### 2. Truy cập từ xa (Remote Access)
Đòi hỏi thiết bị đã được cấu hình địa chỉ IP và có kết nối mạng từ trước. Gồm 2 giao thức chính:
* **Telnet:** Truy cập từ xa nhưng **không mã hóa** dữ liệu. Toàn bộ mật khẩu và câu lệnh truyền đi dưới dạng văn bản thuần (`clear text`), dễ bị hacker bắt gói tin và đọc trộm.
* **SSH (Secure Shell):** Truy cập từ xa và **có mã hóa** dữ liệu thành các ký tự đặc biệt. Độ bảo mật cao, an toàn và là phương thức bắt buộc dùng trong thực tế.

> 🛠️ **Phần mềm hỗ trợ đệm dòng lệnh trên máy tính:**
> * **Putty:** Rất nhẹ, tính năng cơ bản, miễn phí.
> * **SecureCRT:** Phần mềm chuyên dụng cho kỹ sư mạng khi đi làm (hỗ trợ quản lý tab, lưu file cấu hình, chạy script tự động...).

---

## III. Bốn chế độ hoạt động (Modes) trong Cisco IOS
Khi kết nối vào thiết bị Cisco, người quản trị sẽ tương tác qua 4 chế độ có mức độ phân quyền từ ngoài vào trong:

### 1. User EXEC Mode (Chế độ người dùng)
* **Nhận diện:** `Router>` hoặc `Switch>` (Tên thiết bị + Dấu `>`).
* **Đặc điểm:** Chế độ đầu tiên khi đăng nhập. Chỉ xem được một vài thông số cơ bản, **không thể thay đổi** cấu hình thiết bị.

### 2. Privileged EXEC Mode (Chế độ đặc quyền)
* **Nhận diện:** `Router#` hoặc `Switch#` (Tên thiết bị + Dấu `#`).
* **Đặc điểm:** Xem được mọi thông số, mật khẩu, kiểm tra cấu hình đang chạy (`show running-config`), nhưng vẫn **chưa thể thay đổi** cấu hình.

### 3. Global Configuration Mode (Chế độ cấu hình toàn cục)
* **Nhận diện:** `Router(config)#` (Có chữ `(config)` trước dấu `#`).
* **Đặc điểm:** Cho phép nhập các câu lệnh làm thay đổi thông số và cấu hình tác động đến toàn bộ thiết bị (Ví dụ: đổi tên thiết bị, tạo mật khẩu...).

### 4. Sub-configuration Mode (Chế độ cấu hình nhánh/chi tiết)
* **Nhận diện:** `Router(config-if)#` (cấu hình cổng) hoặc `Router(config-line)#` (cấu hình đường truyền).
* **Đặc điểm:** Chế độ cấu hình chi tiết cho một phần nhỏ của thiết bị (Ví dụ: cấu hình IP cho một cổng cụ thể, cài mật khẩu cho đường Console/Telnet).

---

## IV. Các câu lệnh điều hướng và cấu hình cơ bản đầu tiên

### 1. Lệnh di chuyển từ ngoài vào trong
* Chuyển từ *User EXEC* vào *Privileged EXEC*:
```ios
Router> enable
```

* Chuyển từ *Privileged EXEC* vào *Global Configuration*:
```ios
Router# configure terminal
```



### 2. Lệnh di chuyển từ trong ra ngoài

* Lùi lại một chế độ liền trước nó:
```ios
Router(config)# exit
```


* Quay thẳng về chế độ đặc quyền *Privileged EXEC* (dấu `#`) dù đang ở bất kỳ chế độ con sâu đến mức nào:
```ios
Router(config-if)# end
```


*(Hoặc có thể sử dụng tổ hợp phím tắt: `Ctrl + Z` hoặc `Ctrl + C`)*

### 3. Lệnh cấu hình thiết bị

* Đổi tên thiết bị (Bắt buộc phải đứng ở chế độ *Global Configuration Mode*):
```ios
Router(config)# hostname <Tên_Muốn_Đổi>
```



---

## V. Bài Tập Thực Hành Số 1 (Lab ban đầu)

* **Công cụ:** Cisco Packet Tracer, sử dụng Router **2811**.
* **Yêu cầu:** Khởi động thiết bị, gõ `no` để bỏ qua Setup Mode. Vào chế độ cấu hình đổi tên Router thành tên viết tắt của mình (Ví dụ: `TRANQUY`). Sau đó lùi thiết bị về lại chế độ *User EXEC Mode* (hiển thị dấu `>`).
