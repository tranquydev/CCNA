# Day 15: ARP 

## I. Ba quy luật lưu trữ nâng cao của Bảng địa chỉ MAC trên Switch

### 1. Quy luật 1: Cơ chế đếm ngược thời gian lưu trữ (Aging Time)
* Khi Switch học được một địa chỉ MAC nguồn mới lọt vào cổng vật lý, nó lập tức nạp vào bảng Mac-Address-Table kèm theo một đồng hồ đếm ngược với mốc thời gian mặc định chính xác là **300 giây (5 phút)**.
* Đồng hồ sẽ liên tục chạy lùi về mốc 0 giây. Nếu trong suốt 5 phút đó, máy tính Client hoàn toàn im lặng, không phát thêm bất kỳ lưu lượng traffic nào lọt vào cổng Switch, bản ghi đó sẽ bị cưỡng bức xóa bỏ (Remove) ra khỏi bộ nhớ RAM để tiết kiệm tài nguyên hệ thống.
* **Cơ chế làm mới (Reset):** Nếu tại giây thứ 250, máy tính đó phát đi một gói tin bất kỳ lọt vào cổng Switch, Switch nhận diện mã MAC nguồn đã có sẵn, nó không ghi đè bản ghi mới mà lập tức reset đồng hồ đếm ngược quay trở lại mốc tối đa 300 giây để đếm lại từ đầu.

### 2. Quy luật 2: Trạng thái Link Down xóa bỏ bản ghi lập tức
* Thiết bị không nhất thiết phải đợi hết thời gian timeout 300 giây mới dọn bảng MAC. 
* Nếu một cổng vật lý trên Switch bị rút dây cáp mạng hoặc bị tắt bằng lệnh `shutdown` (Trạng thái **Link Down**), Switch lập tức xóa sạch toàn bộ các bản ghi địa chỉ MAC liên quan đến cổng đó ngay tức khắc để giải phóng bộ nhớ.

### 3. Quy luật 3: Ranh giới ánh xạ giữa Cổng (Port) và Địa chỉ MAC
2 câu hỏi bẫy logic phỏng vấn doanh nghiệp lớn:

* **Câu hỏi 1:** *Một địa chỉ MAC có thể xuất hiện đồng thời tại nhiều cổng khác nhau trên một con Switch tại một thời điểm không?*
  * ➔ **Trả lời: KHÔNG THỂ.** Một địa chỉ MAC vật lý tại một thời điểm duy nhất chỉ có quyền nằm ở duy nhất một cổng Switch. Tiến trình rút cáp mạng từ cổng 1 cắm sang cổng 2 thực chất đã làm cổng 1 rơi vào Link Down và xóa bản ghi cũ trước khi học bản ghi mới ở cổng 2. Nếu một bảng MAC ghi nhận 1 địa chỉ MAC nằm ở 2 cổng khác nhau, bảng đó sai logic hoàn toàn hoặc hệ thống đang dính lỗi Loop mạng (Vòng lặp Layer 2).
* **Câu hỏi 2:** *Một cổng vật lý (Port) trên Switch có thể chứa đồng thời nhiều địa chỉ MAC khác nhau không?*
  * ➔ **Trả lời: HOÀN TOÀN CÓ THỂ.** Trong mô hình mạng phân tầng doanh nghiệp thực tế, khi một cổng của Switch 1 được cắm nối tiếp sang cổng của Switch 2 (mô hình cắm tầng nối tiếp). Cổng trung chuyển trung gian đó trên Switch 1 sẽ học toàn bộ mã MAC của hàng chục máy tính PC đang cắm ở Switch 2 truyền qua. Do đó, một dòng Port vật lý hiển thị lưu nhiều dòng địa chỉ MAC là hoàn toàn đúng kỹ thuật.

---

## II. Bản chất và Kịch bản phối hợp các trường thông tin của Giao thức ARP

**Bản chất bài toán:** Khi người dùng đứng từ máy PC1 (IP: `192.168.1.1`, MAC: `A1`) thực hiện lệnh truy cập gõ kết nối mạng đến máy PC2 (IP: `192.168.1.2`, MAC: `A2`). Gói tin dữ liệu ban đầu chứa đầy đủ dải số Layer 3 (IP Source: `.1.1`, IP Dest: `.1.2`) và biết MAC Source: `A1`, nhưng hoàn toàn bị **khuyết ô địa chỉ MAC Đích (MAC Destination)** của máy đối phương.

Gói tin dữ liệu chính (bản tin ICMP) buộc phải đóng băng, giữ lại tại hàng đợi cạc mạng. Để truy tìm mã MAC Đích này lấp vào ô trống, cạc mạng tự động phóng ra bản tin **ARP Request** (Address Resolution Protocol) để phân giải địa chỉ.



### 🛠️ Tiến trình phân tích luồng chạy gói tin ARP chi tiết trên sơ đồ:

#### ➊ Bản tin Chiều đi - ARP Request (Cơ chế Broadcast phát loa rộng rãi)
* Để gói tin ra khỏi cạc mạng, PC1 mượn tạm địa chỉ MAC Broadcast kịch trần gồm 12 ký tự F (**`FFFF.FFFF.FFFF`**) điền vào ô trống MAC Destination.
* **Cấu trúc bản tin ARP Request:**  `IP Source: 192.168.1.1` | `IP Dest: 192.168.1.2`
  * `MAC Source: A1` | `MAC Dest: FFFF.FFFF.FFFF`
* **Hành động của Switch:** Gói tin lọt vào cổng 1. Switch thực thi việc học MAC nguồn `A1` gắn vào cổng 1. Đọc trường MAC Dest thấy mã Broadcast `FFFF.FFFF.FFFF` ➔ Switch chạy lệnh **Flooding** nhân bản đồng loạt phát ra tất cả các cổng còn lại (cổng số 2 và cổng số 3), ngoại trừ cổng 1 nhận vào.

#### 🔄 Phản ứng của các máy tính trạm khi nhận tin Broadcast:
* **Tại máy PC3 (IP: `192.168.1.3`):** Đọc Layer 2 thấy mã MAC Broadcast nên đồng ý bóc vỏ đưa lên Layer 3 kiểm tra. Tại Layer 3, nó đọc thấy trường IP Destination ghi số `192.168.1.2` ➔ không trùng khớp với IP `.1.3` của nó. Máy PC3 lập tức hủy bỏ gói tin (**Drop ngay tại Layer 3**).
* **Tại máy PC2 (IP: `192.168.1.2`):** Đọc Layer 2 thấy mã MAC Broadcast nên đồng ý bóc vỏ đưa lên Layer 3 kiểm tra. Tại Layer 3, đọc thấy trường IP Destination trùng khớp hoàn toàn với IP `.1.2` của mình ➔ PC2 xác nhận gói tin tìm đúng mình và chuyển lên tầng trên đọc nội dung yêu cầu phân giải.

#### ➋ Bản tin Chiều về - ARP Reply (Cơ chế Unicast đường thẳng bảo mật)
* PC2 nạp thông tin ngược lại để tạo bản tin trả lời ARP Reply. Do lúc này đã biết rõ đích danh MAC của máy PC1 (thu được từ gói tin chiều đi), nó đóng gói bản tin theo cơ chế Unicast chuẩn xác:
* **Cấu trúc bản tin ARP Reply:**  `IP Source: 192.168.1.2` | `IP Dest: 192.168.1.1`
  * `MAC Source: A2` | `MAC Dest: A1`
* **Hành động của Switch:** Gói tin lọt vào cổng 2 của Switch. Switch lập tức thực thi việc học MAC nguồn `A2` gắn vào cổng 2. Tiếp theo, đọc trường MAC Dest thấy ghi mã `A1` ➔ Tra cứu bộ nhớ bảng MAC thấy rõ dòng lưu trữ: `A1` nằm ở cổng số 1. Switch thực hiện lệnh chuyển mạch **Forwarding** phóng một đường thẳng Unicast duy nhất ra đúng cổng số 1 để về cạc mạng máy PC1.
* **Kết quả:** PC1 nhận bản tin ARP Reply, ghi nhận chính xác mã MAC phần cứng của máy `192.168.1.2` chính là `A2`. Nó lập tức giải phóng gói tin Ping đang bị đóng băng lúc đầu, điền mã `A2` vào ô trống MAC Destination và phóng đi thông mạng trọn vẹn.

---

## III. Bảng ARP (ARP Table) & Bộ nhớ đệm Lưu trữ thời gian

### 1. Bản chất của ARP Table (ARP Cache)
Sau khi tốn băng thông phát bản tin ARP Request để tìm được MAC của đối phương, máy PC1 không vứt thông tin đó đi mà tự động nạp vào một bảng dữ liệu bộ nhớ đệm nội bộ gọi là **Bảng ARP (ARP Table)** để tái sử dụng cho các gói tin sau.

### 2. Phân biệt hai loại bảng cốt lõi trong Network (Bắt buộc thuộc lòng)

| Đặc điểm so sánh | Bảng MAC (Mac-Address-Table) | Bảng ARP (ARP Table / Cache) |
| :--- | :--- | :--- |
| **Vị trí tồn tại** | Chỉ có trên Thiết bị Layer 2 (**Switch**). | Có trên **mọi thiết bị đầu cuối và định tuyến** (PC, Router, Server, Laptop...). |
| **Nhiệm vụ ánh xạ** | Liên kết giữa: **Địa chỉ MAC $\longleftrightarrow$ Cổng vật lý (Port)** của Switch. | Liên kết giữa: **Địa chỉ IP $\longleftrightarrow$ Địa chỉ MAC** của thiết bị. |
| **Thời gian lưu mặc định**| **300 giây (5 phút)**. | **10 phút** (Tùy thuộc hệ điều hành máy tính/Router). |

### 3. Quy luật reset và xóa bản ghi
* **Quy luật làm mới:** Mỗi khi có một gói tin mới đi vào cổng và trùng khớp với bản ghi đang lưu, đồng hồ đếm ngược của thiết bị sẽ lập tức reset quay trở lại mốc thời gian tối đa ban đầu (300 giây hoặc 10 phút) để tiếp tục đếm ngược. 
* **Xóa do Timeout:** Nếu hết sạch thời gian đếm ngược mà thiết bị không nhận thêm gói tin làm mới nào, nó sẽ cưỡng bức xóa bỏ (Remove) địa chỉ đó ra khỏi bảng để giải phóng dung lượng RAM/CPU.
* **Xóa do Link Down:** Nếu dây cáp vật lý cắm vào cổng bị rút ra (Cổng rơi vào trạng thái Link Down), thiết bị lập tức xóa sạch bản ghi liên quan đến cổng đó ngay lập tức mà không thèm chờ đồng hồ đếm ngược.

---

## IV. Các câu lệnh Quản trị bảng ARP trên hệ điều hành

### 1. Trên hệ điều hành máy tính Windows (Gõ lệnh trong Command Prompt - cmd)
* Xem bảng bộ nhớ đệm ARP hiện tại trên máy tính:
```cmd
 arp -a
```

* Xóa sạch bảng bộ nhớ đệm ARP (ép máy tính phải phát bản tin ARP Request học lại từ đầu):
```cmd
arp -d
```



### 2. Trên chế độ Privileged EXEC Mode (Mode #) của Router Cisco

* Xem bảng bộ nhớ dữ liệu ARP trên Router:
```ios
Router# show arp
```


* Xóa sạch bảng ARP hiện tại trên thiết bị:
```ios
Router# clear arp-cache
```



---

## V. Thực hành Bài tập tư duy Logic 

* **Bản chất số gói tin Ping mặc định:** Trên hệ điều hành Windows, khi thực hiện gõ lệnh ping, cạc mạng máy tính mặc định phóng ra liên tục 4 gói tin độc lập để kiểm tra (Cisco IOS phóng ra 5 gói tin).
* **Giải mã hiện tượng mất gói tin đầu tiên:** Gói tin đầu tiên do chưa biết MAC đích nên hệ thống sẽ báo hiệu timeout/mất gói do phải tốn thời gian chạy tiến trình ARP Request tìm địa chỉ. Từ gói tin thứ 2 trở đi, dữ liệu sẽ đi thẳng bằng lệnh Unicast thông qua bảng bộ nhớ đệm ARP Cache vừa nạp nên đường truyền đạt độ ổn định tuyệt đối.

### 🎯 Nhiệm vụ:

* Học thuộc lòng toàn bộ tiến trình đóng gói dữ liệu của bản tin ARP chiều đi (Request) và bản tin chiều về (Reply) kết hợp nguyên lý 2 bước xử lý của Switch.

