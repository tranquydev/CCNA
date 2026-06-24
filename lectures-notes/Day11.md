# Day 11: IP P5

## I. Chấn chỉnh kỷ luật lớp học & Chữa bài tập về nhà Day 10

### 1. Vạch trần lỗi cẩu thả nghiêm trọng chặn điểm số tối đa
* **Hiện tượng lỗi:** Khi nộp bài giải, chỉ ghi độc nhất dải số nhị phân của phần Host Part hoặc ghi các cụm số thập phân khuyết thiếu, hoàn toàn lược bỏ đi thông số mặt nạ mạng.
* **⚠️ Bài học chấn chỉnh kỷ luật :** "Quá trình các bạn ngồi làm nháp ra giấy có thể viết tắt, viết ngoáy cho nhanh. Nhưng khi đã kết luận đáp án cuối cùng để nộp cho giám thị trong phòng thi quốc tế, hoặc sau này đi làm thực tế chịu trách nhiệm viết file thiết kế quy hoạch hạ tầng nộp lên cho Sếp, địa chỉ IPv4 bắt buộc phải được hiển thị 100% ở dạng số thập phân có đầy đủ nhãn dấu xuộc và số hiệu Prefix đi kèm (Ví dụ chuẩn: `192.168.1.0/26`). 
  
  Trong môi trường doanh nghiệp, không một ai chấp nhận một file thiết kế hạ tầng nửa chữ thô, nửa số nhị phân hay ghi độc nhất một số 0 cô độc. Sự cẩu thả, thiếu chỉn chu chỉ 1 chữ số trong ngành mạng hoàn toàn đủ sức làm sập toàn bộ hệ thống dữ liệu của một ngân hàng, một tập đoàn lớn ngay lập tức!"

### 2. Tuyên dương tinh thần học tập đạt chuẩn
* Ngược lại với lỗi làm ẩu trên, giảng viên tuyên dương bài làm của Long và Trang. 
* Đặc biệt, Long đã thực hiện phân rã các chuỗi bit nhị phân rất khoa học, sử dụng các dấu vạch thẳng để ngăn cách ranh giới vùng mạng và vùng máy một cách rõ ràng, giúp tiến trình tính toán đạt độ chính xác tuyệt đối, không sai lệch bất kỳ một ký tự nào.

---

## II. Nâng cấp Tuyệt kỹ tính nhẩm mạng: Phương pháp Bước Nhảy (Block Size Method)

### 1. Bản chất của việc nâng cấp tư duy
Để bẻ đôi hoặc bẻ tư một khối mạng mẹ lớn thành các khối mạng con có kích thước bằng nhau, kỹ sư mạng chuyên nghiệp không cần phải ngồi viết lại dải nhị phân 0 và 1 dài dòng, thủ công. Thay vào đó, ta áp dụng giải thuật toán học số cộng tịnh tiến dựa trên hằng số Bước nhảy (Block Size).

### 2. Thuật toán triển khai
Bước nhảy của một Prefix chỉ đánh trực tiếp và làm thay đổi giá trị của đúng Octet chứa ranh giới phân tách bit (Octet biến đổi). Tất cả các Octet tầng mạng khóa cứng phía trước được giữ nguyên hoàn toàn.

* **Công thức cốt lõi:** `Bước nhảy mạng (Block Size) = 2^(32 - Prefix)`

### 📋 Bảng hằng số bước nhảy vàng (Kỹ sư bắt buộc phải học thuộc lòng):
* Prefix `/25` ➔ Bước nhảy = 2^(32-25) = 2^7 = **128** (Vùng đuôi chỉ gồm 2 cụm mạng cố định: `.0` và `.128`).
* Prefix `/26` ➔ Bước nhảy = 2^(32-26) = 2^6 = **64** (Vùng đuôi gồm 4 cụm mạng tịnh tiến: `.0`, `.64`, `.128`, `.192`).
* Prefix `/27` ➔ Bước nhảy = 2^(32-27) = 2^5 = **32** (Vùng đuôi nhảy đều cụm cộng thêm 32 đơn vị: `.0`, `.32`, `.64`, `.96`...).
* Prefix `/28` ➔ Bước nhảy = 2^(32-28) = 2^4 = **16** (Vùng đuôi nhảy đều cụm cộng thêm 16 đơn vị: `.0`, `.16`, `.32`, `.48`...).

### 3. Quy tắc gán số tịnh tiến liên hoàn
* Khối mạng con đầu tiên sinh ra luôn luôn mang địa chỉ trùng khớp hoàn toàn với địa chỉ mạng mẹ gốc ban đầu.
* Địa chỉ mạng con kế tiếp được xác định bằng công thức siêu tốc: `Địa chỉ mạng trước + Giá trị Bước nhảy gán vào Octet biến đổi`.

---

## III. Thực hành Giải toán mạng siêu tốc bằng hai phương pháp

### 🛠️ Bài toán 1 (Dạng dịch bit trên cùng 1 Octet): 
* **Đề bài:** Cho dải mạng mẹ `192.168.1.0/24`. Hãy bẻ nhỏ mạng này thành các khối mạng con mang nhãn `/26`.
* **Bước 1 (Tính số khối mạng sinh ra):** Từ /24 nâng lên /26 tức là mượn 2 bit ➔ Hệ thống sinh ra 2^2 = **4 khối mạng con**.
* **Bước 2 (Tìm bước nhảy và tịnh tiến):** Khối mạng /26 sở hữu hằng số bước nhảy = 2^(32-26) = 2^6 = **64**. Tiến hành cộng tịnh tiến đều 64 đơn vị liên tục vào Octet thứ 4:
  * Subnet 1 (Trùng mạng mẹ): **`192.168.1.0/26`** (Chiếm dải từ .0 đến .63)
  * Subnet 2: `.0 + Bước nhảy 64` ➔ **`192.168.1.64/26`** (Chiếm dải từ .64 đến .127)
  * Subnet 3: `.64 + Bước nhảy 64` ➔ **`192.168.1.128/26`** (Chiếm dải từ .128 đến .191)
  * Subnet 4: `.128 + Bước nhảy 64` ➔ **`192.168.1.192/26`** (Chiếm dải từ .192 đến .255)
* ➔ *Kết quả:* Tìm ra ngay đáp án 4 khối mạng con chính xác trong vòng 3 giây không tốn một dòng giấy nháp phân tích nhị phân!

### 🛠️ Bài toán 2 (Dạng bẫy tràn số mạng sang Octet thứ 3):
* **Đề bài:** Cho dải mạng mẹ `192.168.4.0/23`. Hãy bẻ nhỏ dải này thành các khối mạng con mang nhãn `/25`.
* **Bước 1 (Tính số khối mạng sinh ra):** Từ /23 nâng lên /25 tức là mượn 2 bit ➔ Sinh ra 2^2 = **4 khối mạng con**.
* **Bước 2 (Tìm bước nhảy và xử lý bẫy tràn số):** Khối mạng /25 có bước nhảy kịch trần = 2^(32-25) = 2^7 = **128** gán vào Octet thứ 4. Tiến hành cộng tịnh tiến đều 128 đơn vị liên tục:
  * Subnet 1 (Trùng mạng gốc): **`192.168.4.0/25`** (Chiếm dải số từ .4.0 đến .4.127)
  * Subnet 2: `.0 + 128` ➔ **`192.168.4.128/25`** (Chiếm dải số từ .4.128 đến .4.255)
  * Subnet 3: `.128 + 128 = 256` ➔ *⚠️ Bẫy tràn số mạng:* Quy ước cấu trúc địa chỉ IPv4 không tồn tại số hiệu giá trị 256 trong bất kỳ một Octet nào. Bản chất số 256 chính là địa chỉ liền sau của mốc kịch khung `.4.255`. Do đó, theo nguyên lý phần cứng, số hiệu cạc mạng sẽ tự động trả trị số về số `0` và cộng tăng thêm `1` đơn vị vào Octet thứ 3 ở ngay phía trước (Tương tự hiện tượng kim đồng hồ nhảy từ phút sang giờ).
    * Địa chỉ mạng con thứ 3 biến đổi chính xác thành: **`192.168.5.0/25`** (Chiếm dải từ .5.0 đến .5.127)
  * Subnet 4: `.5.0 + 128` ➔ **`192.168.5.128/25`** (Chiếm dải số từ .5.128 đến .5.255)

---

## IV. Bài toán thực tế chặng cuối: Thiết kế quy hoạch mạng VLSM bằng phương pháp Bước Nhảy

**Đề bài toán thiết kế:** Mạng mẹ gốc ban đầu cấp cho Tổng công ty là `192.168.1.0/24`. Người quản trị mạng phải sử dụng tuyệt kỹ Bước Nhảy để quy hoạch mạng con VLSM cho 4 phòng ban có nhu cầu máy trạm hoàn toàn độc lập: 
* **Phòng A:** 50 Hosts
* **Phòng C:** 25 Hosts
* **Phòng D:** 15 Hosts
* **Phòng B:** 8 Hosts

### 📐 Tiến trình nhẩm siêu tốc của Kỹ sư trưởng hạ tầng:

* **Bước 1 (Xếp hàng nhu cầu giảm dần):** Phòng A (50 máy -> cần nhãn /26) ➔ Phòng C (25 máy -> cần nhãn /27) ➔ Phòng D (15 máy -> cần nhãn /27) ➔ Phòng B (8 máy -> cần nhãn /28).
* **Bước 2 (Nhẩm tịnh tiến bước nhảy liên hoàn từ mốc gốc .1.0/24):**
  * **Cấp cho Phòng A (Cần nhãn `/26` -> Bước nhảy tương ứng là 64):** Lấy luôn mốc đầu tiên của dải mạng mẹ ➔ **Subnet Phòng A: `192.168.1.0/26`** (Chiếm trọn dải từ .0 đến .63). Kho tài nguyên thừa kế tiếp của công ty dịch chuyển lên cột mốc số `.64`.
  * **Cấp cho Phòng C (Cần nhãn `/27` -> Bước nhảy tương ứng là 32):** Xuất phát tại điểm cột mốc số `.64` vừa nhận ➔ **Subnet Phòng C: `192.168.1.64/27`** (Chiếm trọn dải từ .64 đến .95). Điểm tài nguyên thừa kế tiếp tịnh tiến lên mốc: `64 + 32 = 96`.
  * **Cấp cho Phòng D (Cần nhãn `/27` -> Bước nhảy tương ứng là 32):** Xuất phát tại điểm cột mốc số `.96` vừa nhận ➔ **Subnet Phòng D: `192.168.1.96/27`** (Chiếm trọn dải từ .96 đến .127). Điểm tài nguyên thừa kế tiếp tịnh tiến lên mốc: `96 + 32 = 128`.
  * **Cấp cho Phòng B (Cần nhãn `/28` -> Bước nhảy tương ứng là 16):** Xuất phát tại điểm cột mốc số `.128` vừa nhận ➔ **Subnet Phòng B: `192.168.1.128/28`** (Chiếm trọn dải từ .128 đến .143).

> 💾 **Đóng gói dữ liệu dự trữ:** Toàn bộ khối dải mạng còn dư thừa tính từ trị số vị trí `.1.144` kéo dài cho đến kịch khung `.1.255` được hệ thống đóng gói, cất kỹ vào kho lưu trữ tài nguyên của doanh nghiệp nhằm mục đích mở rộng quy mô sau này. Toàn bộ nhóm học viên (Công, Nhật, Trang, Huỳnh, Thắng, Long) đều gõ lệnh thực thi nộp kết quả nhẩm chính xác tuyệt đối lên group lớp.

---
