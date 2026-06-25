# Day 14: LAB IP Ethernet switching

## I. Tổng kết thông số mạng phụ: Subnet Mask & Wildcard Mask

### 1. Bản chất của Subnet Mask (Mặt nạ mạng)
Trong suốt chuỗi bài học chia mạng trước đây, chúng ta hoàn toàn dùng số hiệu Prefix (như /24, /26) để khóa cứng phần mạng. Chỉ số Prefix sinh ra là để tối ưu cho tư duy tính toán của con người. 

Ngược lại, Subnet Mask được tạo ra chỉ để phục vụ cho đúng cú pháp cấu hình dòng lệnh CLI trên hệ điều hành của các thiết bị Cisco IOS đời cũ hoặc Windows cũ. 
*(Các hệ điều hành mạng hiện đại như Juniper hay Cisco IOS-XE mới hiện nay đã cho phép loại bỏ hoàn toàn Subnet Mask và hỗ trợ gõ trực tiếp số hiệu Prefix).*

* **Cách tính nhanh Subnet Mask:** Căn cứ theo số hiệu Prefix, ta gán bấy nhiêu bit đầu tiên tính từ trái qua phải bằng số 1, toàn bộ các bit còn lại trong dải 32 bit gán bằng số 0. Cuối cùng, quy đổi cụm nhị phân của từng Octet về lại hệ thập phân:
  * Mạng `/24` ➔ `255.255.255.0`
  * Mạng `/26` ➔ `255.255.255.192`

### 2. Bản chất của Wildcard Mask (Mặt nạ nghịch đảo)
Wildcard Mask là thông số toán học đối lập hoàn toàn của Subnet Mask. Nó đóng vai trò làm bộ lọc điều kiện check lỗi để chặn/cho phép lưu lượng dữ liệu (Access Control List - ACL) hoặc dùng để khai báo phân vùng định tuyến động OSPF ở các chương học nâng cao nâng cấp.

* **Công thức nhẩm tính nhanh:** `Wildcard Mask = 255.255.255.255 - Subnet Mask`
  *(Kỹ sư thực hiện phép trừ tịnh tiến độc lập trên từng Octet một).*
* *Ví dụ:* Tìm Wildcard Mask của khối mạng `/26` (có Subnet Mask tương ứng là `255.255.255.192`):
  * Octet 1, 2, 3: `255 - 255 = 0`
  * Octet 4: `255 - 192 = 63`
  * ➔ **Wildcard Mask: `0.0.0.63`**

---

## II. Bản chất phần cứng của Địa chỉ MAC (Media Access Control)

### 1. Định nghĩa
Nếu địa chỉ IP là địa chỉ logic có thể thay đổi linh hoạt theo sơ đồ mạng thì địa chỉ MAC là địa chỉ vật lý duy nhất, được đúc chết vào chip phần cứng của cạc mạng (NIC - Network Interface Card) ngay từ trong nhà máy bởi nhà sản xuất và hoàn toàn không thể thay đổi. Mọi linh kiện điện tử khi xuất xưởng ra thị trường (Cạc mạng PC, laptop, chuột, bàn phím, điện thoại, tivi) bắt buộc phải mang một địa chỉ MAC cố định để định danh phần cứng.

### 2. Cấu trúc phần cứng
Địa chỉ MAC được cấu tạo từ **48 bit nhị phân**, biểu diễn ra giao diện quản trị bên ngoài dưới dạng hệ Hexadecimal (Thập lục phân) dài đúng 12 ký tự. Giữa các cụm ký tự thường được phân cách bởi dấu chấm (`.`) hoặc dấu gạch ngang (`-`).

> 🎯 **Quy đổi cố định phần cứng:** 1 ký tự bit hệ Hexa = Đúng 4 bit nhị phân (Binary) ➔ Tổng cộng địa chỉ MAC dài 12 ký tự Hexa.

### 3. Cấu trúc phân rã 2 vùng của một địa chỉ MAC
Một địa chỉ MAC 12 ký tự Hexa luôn được bẻ đôi thành 2 phân vùng quản trị độc lập:



* **Vùng 1 - OUI (Organizationally Unique Identifier):** Chiếm đúng **6 ký tự Hexa đầu tiên**. Đây là mã số định danh bản quyền mà các tập đoàn công nghệ lớn (Cisco, Dell, Apple, Intel, HP...) bắt buộc phải bỏ tiền ra mua từ tổ chức quản lý địa chỉ toàn cầu IANA (tại Việt Nam do cơ quan VNNIC quản lý).
  * *Ví dụ:* Mọi cạc mạng do Intel sản xuất ra thị trường bắt buộc phải mang 6 ký tự đầu giống hệt nhau là `F4:A4:75:...`
* **Vùng 2 - NIC:** Chiếm **6 ký tự Hexa còn lại ở phía sau**. Phân dải số này do chính hãng sản xuất tự quy ước gán tăng dần một cách tuần tự để phân biệt các linh kiện phần cứng khác nhau được đúc ra trong nhà máy.

*Lệnh tra cứu địa chỉ MAC vật lý trên máy tính Windows (Gõ trong Command Prompt - cmd):*
```cmd
ipconfig /all
```

➔ Địa chỉ MAC thực tế của cạc mạng sẽ hiển thị chi tiết tại dòng **`Physical Address`**.

---

## III. Vai trò cốt lõi của Thiết bị chuyển mạch Switch Layer 2

### 1. Định nghĩa chức năng phần cứng

Switch là thiết bị trung gian kết nối mạng hoạt động nòng cốt tại Tầng liên kết dữ liệu (Data Link Layer - Layer 2) của mô hình tham chiếu OSI.

Công năng chính yếu nhất của Switch là làm cửa ngõ trung tâm kết nối để gom tất cả các máy tính trạm Client (PC, Laptop, Máy in, Camera, Server) thuộc cùng một phân vùng mạng mạng nội bộ LAN lại với nhau. Bản chất Switch đóng vai trò như một chiếc ổ cắm chia điện thông minh kịch trần gồm rất nhiều cổng Port vật lý kết nối (phổ biến nhất là dòng Switch 24 Port hoặc 48 Port).

### 2. Bản chất dải số IP gán trên Switch

Hệ điều hành của thiết bị Switch Layer 2 vận hành tiến trình chuyển mạch hoàn toàn dựa trên địa chỉ MAC chứ nó **không hiểu địa chỉ IP**.

Tuy nhiên, trong thực tế làm việc, kỹ sư mạng vẫn cấu hình gán một địa chỉ IP ảo nạp vào cổng Interface ảo VLAN 1 của Switch. Địa chỉ IP này sinh ra hoàn toàn không phục vụ cho việc truyền dẫn luồng dữ liệu của người dùng, mà nó chỉ dùng làm địa chỉ đích để kỹ sư từ xa gõ lệnh Telnet/SSH đăng nhập vào quản trị, cấu hình và giám sát thiết bị Switch từ xa qua mạng.

---

## IV. Ma trận Vận hành Dòng chảy Gói tin qua Bộ nhớ Switch

Khi máy tính PC1 phóng gói dữ liệu Ping (ICMP Request) tới máy tính PC2 chạy xuyên qua một con Switch mới khởi động (bộ nhớ bảng lưu trữ địa chỉ MAC đang trống trơn), Switch sẽ kích hoạt quy trình xử lý gồm 2 bước bất biến chạy ngầm sau:

### ➊ Bước 1: Học địa chỉ nguồn để Xây dựng bảng MAC (Mac-Address-Table)

* Gói tin Ping của PC1 lọt vào cổng Port vật lý số 1 của Switch. Switch lập tức chạy lệnh bóc vỏ lớp vỏ Layer 2 (De-encapsulation), đọc trường địa chỉ nguồn **MAC Source** và ghi nhận được mã `MAC_PC1`.
* Switch đối chiếu mã này vào bảng bộ nhớ của nó. Do bảng lưu trữ đang trống, nó lập tức nạp một dòng ánh xạ vật lý cố định vào bộ nhớ RAM:
* **Địa chỉ `MAC_PC1` ➔ Liên kết cố định với Port vật lý số 1 của Switch**


* *Ý nghĩa:* Tiến trình này giúp Switch ghi nhớ và định vị được vị trí cắm dây phần cứng hiện tại của máy PC1.

### ➋ Bước 2: Tra cứu địa chỉ đích để Thực hiện Chuyển mạch (Forwarding)

* Sau khi hoàn thành bước học nguồn, Switch tiếp tục đọc đến trường địa chỉ đích **MAC Destination** trong gói tin để tìm đường đẩy dữ liệu ra ngoài. Nó thấy ghi mã địa chỉ đích là `MAC_PC2`.
* Switch thực hiện tra cứu bảng bộ nhớ Mac-Address-Table để tìm cổng Port đầu ra tương ứng. Do máy PC2 chưa phát tin lên mạng lần nào từ khi Switch bật, bảng bộ nhớ đang khuyết thiếu thông tin của `MAC_PC2`.
* **Thuật toán xử lý lỗi khuyết địa chỉ (Flooding - Phát tán b bão mạng):** Vì không biết máy PC2 đang cắm dây ngồi ở cổng nào, Switch lập tức nhân bản gói tin đó ra thành nhiều bản sao và phóng đồng loạt ra tất cả các cổng Port vật lý còn lại trên thiết bị (cổng số 2, cổng số 3...), ngoại trừ cổng Port số 1 vừa nhận tin vào.

#### 🔄 Phản ứng của các máy trạm nhận được bão tin Flooding:

* **Tại máy PC3 (Cắm ở cổng số 3):** Máy nhận gói tin, bóc vỏ Layer 2 thấy trường *MAC Destination* ghi số hiệu của PC2 ➔ Đối chiếu thấy không trùng khớp với mã MAC cạc mạng phần cứng của nó ➔ PC3 lập tức cưỡng bức hủy bỏ gói tin (**Drop ngay tại Layer 2**) để bảo vệ tài nguyên máy.
* **Tại máy PC2 (Cắm ở cổng số 2):** Máy nhận gói tin, đối chiếu thấy trùng khớp hoàn toàn với mã MAC vật lý phần cứng của nó ➔ PC2 đồng ý nhận dữ liệu và chuyển lên tầng trên xử lý bản tin.

### ➌ Tiến trình gửi gói tin phản hồi ngược về (ARP / ICMP Reply)

* Máy PC2 tiến hành tạo gói tin phản hồi trả ngược về cho PC1 với thông số dải số được đổi chiều: MAC Source: `MAC_PC2` | MAC Destination: `MAC_PC1`.
* Gói tin lọt vào cổng Port số 2 của Switch. Switch lập tức thực thi lại quy trình ngầm:
* **Thực thi Bước 1 (Học nguồn):** Đọc trường *MAC Source* biết được `MAC_PC2` đang cắm dây tại cổng số 2 ➔ Nạp dòng ánh xạ thứ hai vào bảng bộ nhớ Mac-Address-Table.
* **Thực thi Bước 2 (Tra đích):** Đọc trường *MAC Destination* thấy ghi mã `MAC_PC1` ➔ Tra bảng bộ nhớ và phát hiện ngay dòng lưu trữ đã học được từ chặng đi: `MAC_PC1` đang nằm ở cổng Port số 1.


* **Chuyển mạch đích danh (Forwarding):** Lúc này, Switch không chạy lệnh phát tán bão mạng Flooding nữa, mà nó thực hiện lệnh Forwarding phóng một đường thẳng **Unicast** duy nhất ra đúng cổng Port số 1 để trả dữ liệu về chính xác cạc mạng của máy PC1. Phiên kết nối kết thúc hoàn hảo.

---

## V. Các câu lệnh Quản trị dữ liệu MAC vật lý trên Switch

Kỹ sư mạng thực hiện gõ các câu lệnh sau tại chế độ Đặc quyền Privileged EXEC Mode (Mode dấu `#`) của Switch Cisco để kiểm soát và giám sát thông số phần cứng thiết bị:

* **Lệnh xem bảng hiển thị địa chỉ MAC đang lưu trong bộ nhớ RAM:**
```ios
Switch# show mac-address-table
```


* **Lệnh xóa sạch hoàn toàn bộ nhớ bảng MAC động (Ép Switch phải chạy lại tiến trình học và xây dựng bảng dữ liệu từ đầu):**
```ios
Switch# clear mac-address-table dynamic
```



---

## VI. Bài tập thực hành 

* **Nhiệm vụ Lab:** Tự thiết kế và kéo một sơ đồ mạng cơ bản gồm 1 thiết bị Switch và 3 máy trạm PC cắm dây vào các cổng vật lý tương ứng trên phần mềm giả lập Cisco Packet Tracer.
* **Yêu cầu thực thi:** Bật tính năng giả lập dòng chảy **Simulation Mode**, tiến hành gõ lệnh Ping để tự kiểm tra, quan sát bằng mắt tiến trình nạp địa chỉ, cơ chế Flood nhân bản gói tin phát tán bão mạng của gói tin chặng đi đầu tiên và lệnh Forwarding phóng đường thẳng Unicast của gói tin phản hồi chặng về.
* **Yêu cầu lý thuyết:** Học thuộc lòng bản chất 2 bước xử lý gói tin của Switch (Bước 1: Học MAC nguồn; Bước 2: Tra bảng MAC đích).
