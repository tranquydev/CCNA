# Day 5: Mô Hình Tham Chiếu OSI 7 Tầng & Tiến Trình Đóng Gói Dữ Liệu

## I. Ba phương thức Truyền thông căn bản trong Network

Trong hạ tầng mạng máy tính, mọi luồng lưu lượng dữ liệu đều được truyền tải qua 1 trong 3 kiểu cốt lõi sau:

* **Unicast (Truyền thông 1 - 1):** Gói tin đi đích xác từ một nguồn phát tới duy nhất một thiết bị nhận.
  * *Ví dụ:* Giảng viên gửi tin nhắn riêng cho một học viên trên Zalo.
* **Multicast (Truyền thông 1 - Nhóm):** Gói tin đi từ một nguồn phát tới một nhóm thiết bị đích được chỉ định trước (hoàn toàn không gửi cho tất cả mọi người).
  * *Ví dụ:* Phát thông báo nhắc nhở riêng cho 2 học viên trong lớp, các thành viên khác không nhận được. Đây là công nghệ nền tảng ứng dụng mạnh trong truyền hình cáp, IPTV (VTV, VTC...).
* **Broadcast (Truyền thông 1 - Tất cả):** Gói tin phóng đi từ một thiết bị và gửi đồng loạt tới toàn bộ tất cả mọi thiết bị thành viên nằm trong cùng một phân vùng mạng LAN/Subnet.
  * *Ví dụ:* Một tin nhắn gửi vào nhóm chung chứa toàn bộ học viên trong lớp. Giao thức phân giải địa chỉ ARP sau này sẽ dùng Broadcast làm thuật toán nền tảng.

---

## II. Vai trò Lịch sử và Cấu trúc 7 tầng của Mô hình OSI

### 1. Lý do mô hình OSI ra đời
Vào thuở sơ khai của ngành mạng, do không có một bộ tiêu chuẩn chung, mỗi hãng công nghệ (Dell, HP, IBM...) tự sản xuất phần cứng và cổng cắm theo quy ước độc quyền riêng. Thiết bị của hãng này không thể cắm vừa hoặc hiểu dữ liệu của hãng khác, gây ra sự độc quyền và kìm hãm công nghệ. 

Tổ chức quốc tế đã ban hành mô hình **OSI (Open Systems Interconnection)** làm bộ luật tiêu chuẩn chung toàn cầu buộc mọi hãng phần cứng phải tuân theo. Nhờ đó, cạc mạng của Asus, màn hình LG, chip Intel đều có thể lắp ráp và giao tiếp hoàn hảo trên cùng một thùng máy PC.

### 2. Ý nghĩa phân rã lớp (Chuyên môn hóa)
OSI chia nhỏ hệ thống thành 7 tầng riêng biệt từ cao xuống thấp để thực hiện "chuyên môn hóa" công việc, giúp các hãng công nghệ đào sâu nghiên cứu tối ưu hóa thiết bị:
* *Cisco / Juniper:* Tập trung làm chip định tuyến, xử lý tối ưu cho Layer 2, Layer 3, Layer 4.
* *Microsoft / Apple:* Tập trung viết ứng dụng hệ điều hành cho Layer 7.

### 3. Danh mục 7 Layer OSI (Từ trên xuống dưới)



* **Layer 7 - Application Layer (Tầng ứng dụng):** Cung cấp giao diện đồ họa/phần mềm cho người dùng (Zalo, Facebook, Outlook, Web Browser...) để con người tương tác gõ câu chữ tạo ra dữ liệu thô. Đây là vùng hoạt động chính của Developer.
* **Layer 6 - Presentation Layer (Tầng trình diễn):** Chịu trách nhiệm định dạng, quy chuẩn đuôi file dữ liệu (`.mp3`, `.mp4`, `.jpg`, `.doc`, `.rar`...) giúp mọi hệ điều hành khác nhau (Windows, MacOS, Linux) đều giải mã và đọc hiểu được.
* **Layer 5 - Session Layer (Tầng phiên):** Thực hiện tiến trình 3 bước: Thiết lập, duy trì, và giải phóng (kết thúc) phiên kết nối ngầm giữa máy nguồn và máy đích. Giúp máy tính chạy song song được nhiều tác vụ (Ví dụ: tab Facebook trò chuyện không làm ngắt tiếng nhạc của tab YouTube đang phát bên cạnh).
* **Layer 4 - Transport Layer (Tầng giao vận):** Quản lý cơ chế truyền tải gói tin qua 2 giao thức cốt lõi:
  * `TCP`: Bắt tay thiết lập kết nối an toàn, đánh số thứ tự mảnh dữ liệu, truyền lại khi bị rơi mất, ưu tiên tính toàn vẹn dữ liệu tuyệt đối.
  * `UDP`: Truyền tốc độ cao, không bắt tay, mất gói bỏ qua, tối ưu cho dữ liệu nhạy cảm về thời gian thực (Voice IP, Livestream Video).
* **Layer 3 - Network Layer (Tầng mạng):** Chịu trách nhiệm gán địa chỉ logic (Địa chỉ IP) và xử lý thuật toán định tuyến (**Routing** - tìm đường đi tối ưu và xác định hướng đi chính xác nhất cho gói tin xuyên qua mạng internet toàn cầu). Đây là vùng lõi trọng tâm của kỹ sư mạng.
* **Layer 2 - Data Link Layer (Tầng liên kết dữ liệu):** Xử lý luồng dữ liệu mạng LAN nội bộ, thực hiện đóng gói kiểm tra lỗi dữ liệu toàn diện trước khi đẩy xuống dây cáp vật lý thông qua trường đuôi kiểm lỗi Frame Trailer (FCS).
* **Layer 1 - Physical Layer (Tầng vật lý):** Quản lý toàn bộ cấu trúc cơ cáp vật lý (cáp đồng, cáp quang, cổng cắm, xung điện, ánh sáng quang) để biến đổi toàn bộ dữ liệu thành chuỗi mã bit nhị phân (0 và 1) truyền đi trên dây cáp.

---

## III. Tiến trình Đóng gói Dữ liệu (Data Encapsulation) tại Máy phát

Thiết bị phát (Máy nguồn/Source) luôn luôn là thiết bị chịu trách nhiệm chạy tiến trình đóng gói dữ liệu **Data Encapsulation** tuần tự từ tầng 7 xuống tầng 1 theo các bước sau:

```text
      [ Người dùng gõ dữ liệu Chat/Web tại Layer 7, 6, 5 ]
                                │
                                ▼
                        DỮ LIỆU THÔ (DATA)
                                │
                                ▼
         【 BƯỚC 1: LỌT XUỐNG TẦNG GIAO VẬN LAYER 4 】
    - Hành động: Bọc thêm nhãn tiêu đề Layer 4 Header (TCP/UDP)
    - Đơn vị dữ liệu mới gọi là: PHÂN ĐOẠN (SEGMENT)
                                │
                                ▼
           【 BƯỚC 2: LỌT XUỐNG TẦNG MẠNG LAYER 3 】
    - Hành động: Bê nguyên Segment, bọc thêm tiêu đề Layer 3 Network Header (Chứa IP Source/IP Dest)
    - Đơn vị dữ liệu mới gọi là: GÓI TIN (PACKET)
                                │
                                ▼
       【 BƯỚC 3: LỌT XUỐNG TẦNG LIÊN KẾT DỮ LIỆU LAYER 2 】
    - Hành động: Bê nguyên Packet, bọc kẹp đầu bằng Layer 2 Header (Mã MAC)
      và bọc kẹp đuôi bằng trường kiểm lỗi Frame Trailer.
    - Đơn vị dữ liệu mới gọi là: KHUNG (FRAME)
                                │
                                ▼
          【 BƯỚC 4: LỌT XUỐNG TẦNG VẬT LÝ LAYER 1 】
    - Hành động: Ép toàn bộ khung Frame biến đổi thành các tín hiệu xung điện/ánh sáng.
    - Đơn vị dữ liệu gọi là: CÁC BIT NHỊ PHÂN (BITS 0 & 1) phóng lên dây cáp.

```
