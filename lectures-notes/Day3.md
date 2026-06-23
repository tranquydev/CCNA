# Day 3: OS and Basic config P2

## I. Ôn tập mô hình Quản trị thiết bị mạng & Mode CLI

Để cấu hình và quản trị một thiết bị mạng Cisco (Router, Switch, Firewall), kỹ sư sử dụng 2 phương thức tiếp cận giao diện Shell:
* **Giao diện đồ họa (GUI):** Thân thiện, dễ học thông qua các thao tác click chuột (Ví dụ: Windows, Android, trang cấu hình Web của Modem Wifi tại nhà). Tuy nhiên, GUI bị giới hạn và không thể can thiệp sâu.
* **Giao diện dòng lệnh (CLI):** Yêu cầu phải có kiến thức chuyên sâu để điều khiển thiết bị bằng cấu trúc lệnh, nhưng cực kỳ linh hoạt và tối ưu.

> ⚠️ **Khi nào bắt buộc phải dùng cáp Console vật lý?**
> Mọi thiết bị mạng, thiết bị bảo mật (Tường lửa) hay Server mới mua xuất xưởng, hoặc khi cần cài đặt lại hệ điều hành (Boot lại OS), bắt buộc phải sử dụng phương pháp cắm dây Console trực tiếp ở khoảng cách ngắn. Vì lúc này thiết bị hoàn toàn trống trơn, chưa được cấu hình địa chỉ IP hay các dịch vụ quản trị từ xa (Telnet/SSH) để kết nối qua mạng.

### Ba chế độ (Modes) hoạt động cốt lõi của Cisco IOS:
1. **User EXEC Mode (`Router> `):** Chế độ người dùng cơ bản. Chỉ xem được một vài thông số rất hạn chế, hoàn toàn không có quyền thay đổi cấu hình.
2. **Privileged EXEC Mode (`Router# `):** Chế độ đặc quyền (truy cập bằng lệnh `enable`). Cho phép hiển thị toàn bộ thông số cấu hình sâu của phần cứng và hệ thống qua các lệnh `show`, nhưng vẫn không có quyền chỉnh sửa thông số mạng.
3. **Global Configuration Mode (`Router(config)# `):** Chế độ cấu hình toàn cục (truy cập bằng lệnh `configure terminal`). Tại đây chính thức có quyền thực thi các lệnh thay đổi, thiết lập thông số cho toàn bộ thiết bị.

---

## II. Các phím tắt và Quy tắc gõ lệnh bắt buộc 

* **Phím `Tab` (Tự động hoàn thiện từ):** Giúp hoàn thành chữ cái câu lệnh một cách nhanh và chính xác nhất, tránh gõ sai chính tả.
  * *Ví dụ:* Gõ `conf` rồi ấn `Tab` $\rightarrow$ CLI tự điền đầy đủ chữ `configure`.
* **Ký tự dấu hỏi chấm `?` (Cứu cánh dòng lệnh):** Dùng để hiển thị tất cả các từ khóa có thể gõ tiếp theo của cú pháp câu lệnh hiện tại. 
  * *Ví dụ:* Gõ `c?` sẽ hiện ra toàn bộ danh sách lệnh bắt đầu bằng chữ `c` (`clear`, `clock`, `configure`...).
* **Thuật toán nhận diện phím tắt:** CLI chỉ cho phép bấm `Tab` hoặc chấp nhận lệnh viết tắt khi bạn đã gõ vào vừa đủ số lượng chữ cái để phân biệt **duy nhất** từ đó với các từ còn lại trong danh mục.
  * *Ví dụ:* Có 3 từ bắt đầu bằng `co` (`configure`, `connect`, `copy`). Nếu chỉ gõ `co` rồi bấm `Tab` hệ thống sẽ báo lỗi. Nhưng khi gõ tới `conf` (chữ `f` giúp phân biệt hoàn toàn), ấn `Tab` sẽ bung lệnh thành công.

### 🚨 Quy tắc làm Lab bắt buộc 
Hệ điều hành Cisco IOS cho phép viết tắt câu lệnh (Ví dụ: thay vì gõ `configure terminal`, gõ `conf t` rồi ấn `Enter` thiết bị vẫn thực thi). 
**Quy định nghiêm ngặt:** bắt buộc phải gõ bằng lệnh viết tắt hoặc sử dụng phím `Tab`. Mục đích nhằm tối ưu hóa 

* **Phím mũi tên trái/phải:** Điều khiển con trỏ di chuyển để sửa chữ cái trong dòng lệnh.
* **Phím mũi tên lên (`↑`):** Gọi ngược lại các câu lệnh vừa gõ trước đó lưu trong bộ nhớ đệm (History buffer), giúp không phải gõ lại các cấu trúc lệnh dài phức tạp.

---

## III. Các lệnh thiết lập Bảo mật và Mã hóa mật khẩu toàn diện

### 1. Đặt mật khẩu cửa ngõ đặc quyền (Privileged Mode)
Để ngăn chặn người lạ cắm cáp xem trộm file cấu hình hệ thống, cần đặt password chặn ngay tại cửa ngõ chuyển từ *User Mode* lên *Privileged Mode*. Có 2 câu lệnh thực thi:

* **Lệnh văn bản thuần:** `enable password <mật_khẩu>` (Ví dụ: `enable password ccna`).
  * ❌ *Nhược điểm:* Khi gõ lệnh xem cấu hình `show running-config` (`sh run`), mật khẩu sẽ hiển thị rõ ràng dưới dạng chữ thô (Plain Text). Bất kỳ ai đứng cạnh nhìn vào màn hình đều đọc được.
* **Lệnh mã hóa:** `enable secret <mật_khẩu>` (Ví dụ: `enable secret 123`).
  * *Ưu điểm:* Hệ điều hành tự động chạy thuật toán mã hóa mật khẩu này thành một chuỗi ký tự đặc biệt vô nghĩa. Khi `sh run`, mật khẩu gốc được giữ an toàn tuyệt đối.

> 🎯 **Quy luật ưu tiên của Cisco:** > Trong trường hợp đặt cả 2 câu lệnh mật khẩu trên cùng một thiết bị, Cisco IOS sẽ nâng cao tính bảo mật bằng cách **vô hiệu hóa** câu lệnh `enable password` và chỉ chấp nhận duy nhất mật khẩu của câu lệnh mã hóa `enable secret` làm chìa khóa mở cổng.

*Lưu ý khi nhập mật khẩu trên CLI:* Con trỏ chuột sẽ đứng im hoàn toàn, không hiển thị dấu chấm tròn hay dấu sao để chống lộ độ dài mật khẩu, chỉ cần gõ chuẩn ký tự và ấn Enter.

### 2. Đặt mật khẩu cổng cắm dây vật lý (Console Password)
Gia cố thêm lớp bảo mật khóa ngay từ màn hình khởi động đầu tiên (chưa cho lọt vào User Mode) đối với bất kỳ ai mang dây cáp Console đến cắm trực tiếp vào thiết bị:

```ios
Router(config)# line console 0
Router(config-line)# password <mật_khẩu_muốn_đặt>
Router(config-line)# login
```

*(Lệnh `login` là bắt buộc để cưỡng bức thiết bị kiểm tra password mỗi khi có kết nối vào).*

### 3. Cưỡng bức mã hóa toàn bộ mật khẩu trên hệ thống

Mặc định, mật khẩu của cổng Console hay mật khẩu của lệnh `enable password` đều hiện chữ thô. Để ép Router/Switch tự động quét và mã hóa toàn bộ tất cả các ô password đang để chữ thô thành dạng mật khẩu mã hóa Type 7 bảo mật, sử dụng lệnh:

```ios
Router(config)# service password-encryption
```

---

## IV. Lệnh phá băng lỗi Treo dòng lệnh & Tạo biểu ngữ Cảnh báo

### 1. Cách phá trạng thái đơ, treo thiết bị (Treo dịch tên miền)

* **Hiện tượng lỗi:** Khi đứng tại User/Privileged Mode mà gõ nhầm một ký tự vô nghĩa (Ví dụ gõ nhầm chữ `conff` rồi ấn Enter), Router sẽ hiểu lầm ký tự lỗi đó là một "Tên miền" của một máy chủ và tự động phát tin Broadcast đi tìm kiếm khắp nơi. Thiết bị sẽ bị đóng băng (Treo CLI), phím Enter hay `Ctrl + C` đều vô tác dụng. Màn hình hiển thị: `Translating "xxx"... domain server (255.255.255.255)`.
* **Cách bẻ gãy trạng thái đơ ngay lập tức trong 1 giây:** Bấm đồng thời cụm tổ hợp phím đặc biệt:

$$\mathbf{\text{Ctrl}} + \mathbf{\text{Shift}} + \mathbf{\text{6}}$$



### 2. Cấu hình biểu ngữ chào mừng / Cảnh báo pháp lý (Banner MOTD)

Dùng để hiển thị một bức thông điệp hoặc lời răn đe pháp lý đập thẳng vào mắt người truy cập trước khi họ tiến hành nhập password đột nhập vào cổng User Mode.

* **Cú pháp:** `banner motd <ký_tự_đặc_biệt> <Nội_dung_thông_điệp> <ký_tự_đặc_biệt>`
* **Quy ước:** Ký tự đặc biệt nào làm điểm mở đầu (Ví dụ: `#`, `%`, `"`) thì bắt buộc phải dùng chính ký tự đó làm điểm chốt kết thúc.

```ios
Router(config)# banner motd # CANH BAO: Day la thiet bi cua Ngan Hang. Moi hanh vi xam nhap trai phep se bi truy to phap luat! #
```

*Mẹo viết banner xuống dòng:* Sau khi gõ `banner motd #` và ấn Enter, bạn gõ các dòng văn bản tự do, cuối cùng gõ dấu `#` ở dòng cuối cùng và ấn Enter để kết thúc.

### 3. Lệnh hủy bỏ, xóa lệnh cấu hình cũ (Lệnh `no`)

Để xóa bỏ hoàn toàn bất kỳ một câu lệnh nào đã cấu hình trước đó, quy tắc chung của Cisco IOS là: **Đi vào đúng mode chứa câu lệnh đó, gõ lại câu lệnh cũ và thêm chữ `no` vào trước đầu câu lệnh**.

* *Ví dụ trả tên thiết bị về mặc định:* `no hostname`
* *Ví dụ hủy mật khẩu đặc quyền:* `no enable secret`

---

## V. Bài Tập Thực Hành Tại Lớp (Day 3 Lab)

* **Yêu cầu:** Triển khai trên Router 2811. Thực hiện đổi tên Router bằng lệnh viết tắt, cấu hình đồng thời mật khẩu `enable secret`, mật khẩu cổng `line console 0` và cấu hình `banner motd`.
* **Minh chứng hoàn thành:** Gõ `exit` thoát hẳn ra ngoài màn hình đen kịch khung để kiểm tra quy trình bảo mật 3 lớp: hiện biểu ngữ cảnh báo banner $\rightarrow$ bắt nhập password Console mới cho lọt vào User Mode $\rightarrow$ gõ lệnh `enable` bắt nhập password đặc quyền mới cho vào chế độ Privileged EXEC.
*(Trạng thái bài tập: Đã cấu hình và nộp bài trên nhóm đạt chuẩn Correct 100%).*

---
