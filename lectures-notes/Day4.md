# Day 4: Basic config P3

## I. Ôn tập & Cơ chế chống lỗi treo dòng lệnh trên Cisco IOS

* **Cơ chế treo CLI:** Trên hệ điều hành Cisco IOS, nếu người quản trị vô tình gõ sai cấu trúc cú pháp ở *User EXEC Mode* hoặc *Privileged EXEC Mode*, thiết bị sẽ tự động hiểu lầm từ gõ sai đó là một "Tên miền mạng" và phát tin Broadcast đi tìm kiếm ngầm khắp nơi.
* **Hệ quả:** Tiến trình này làm đóng băng và khóa chặt dòng lệnh điều khiển, khiến bạn gõ phím `Enter` hay `Ctrl + C` đều không có tác dụng.
* **Giải pháp phá băng:** Nhấn tổ hợp phím tắt đặc biệt:
$$\mathbf{\text{Ctrl}} + \mathbf{\text{Shift}} + \mathbf{\text{6}}$$
Hệ thống sẽ lập tức cưỡng bức ngắt tiến trình tìm kiếm và trả lại quyền gõ lệnh cho bạn ngay tức thì trong 1 giây.

---

## II. Bản chất cấu trúc 3 tầng Bộ nhớ của Thiết bị Cisco (RAM, NVRAM, Flash)

Để làm chủ thiết bị, kỹ sư cần phân biệt rõ bản chất liên kết của 3 loại bộ nhớ hạ tầng sau:

### 1. Bộ nhớ RAM (Random Access Memory) - Vùng cấu hình tạm thời
* **Bản chất:** Là bộ nhớ lưu trữ tạm thời của phần cứng khi thiết bị hoạt động.
* **Tài liệu quản lý:** Chứa tệp tin dữ liệu dữ liệu **`running-config`** (Cấu hình đang chạy).
* **Cơ chế hoạt động:** Mỗi khi bạn gõ bất kỳ một lệnh thiết lập nào (Ví dụ: `hostname S1`, `service password-encryption`), lệnh đó lập tức nạp trực tiếp vào tệp `running-config` trong RAM để thiết bị thực thi ngay.
* **⚠️ Rủi ro nghiêm trọng:** Do RAM là bộ nhớ khả biến (Volatile), nếu thiết bị đột ngột bị mất điện, nháy điện hoặc gõ lệnh khởi động lại (`reload`), toàn bộ tệp `running-config` sẽ **bốc hơi sạch 100%**, thiết bị quay về trạng thái trắng nguyên bản.

### 2. Bộ nhớ NVRAM (Non-Volatile RAM) - Kho lưu trữ dài hạn
* **Bản chất:** Là bộ nhớ bất biến (không bị mất dữ liệu khi ngắt nguồn điện, tương tự phân vùng ổ cứng ROM trên điện thoại).
* **Tài liệu quản lý:** Quản lý tệp tin **`startup-config`** (Cấu hình khởi động).
* **Cơ chế hoạt động:** Khi Switch/Router được bật nguồn hoặc hoàn thành reboot, chip xử lý sẽ tự động truy cập vào bộ nhớ NVRAM, đọc tệp `startup-config` để lấy lại toàn bộ cấu hình cũ nạp lên bo mạch vận hành hệ thống mạng.

### 3. Bộ nhớ Flash - Ổ cứng chứa Hệ điều hành mạng
* **Bản chất:** Là phân vùng ổ cứng chuyên dụng có dung lượng lớn.
* **Chức năng:** Là nơi lưu trữ tệp tin Hệ điều hành mạng **Cisco IOS** (file định dạng đuôi `.bin`) cùng một số tệp tin quản lý cơ sở dữ liệu đặc biệt khác.

---

## III. Các nhóm lệnh Sao lưu và Khôi phục cấu hình phần cứng

Để phòng chống rủi ro mất cấu hình khi văn phòng dính sự cố sập nguồn điện, kỹ sư phải đồng bộ dữ liệu từ RAM sang NVRAM:

### 1. Nhóm lệnh lưu cấu hình (Thực hiện tại Privileged Mode `#`)
* **Lệnh đầy đủ bản chất:** 
```ios
  Router# copy running-config startup-config

```

*(Có tác dụng nhân bản toàn bộ câu lệnh đang chạy trong RAM để ghi đè vào file khởi động lưu tại NVRAM. Lệnh này có thể mở rộng để backup cấu hình đẩy lên các máy chủ xa như TFTP Server).*

* **Lệnh gõ tắt siêu tốc hành (Thực tế hay dùng):**

```ios
Router# write
```

*(Hoặc gõ tắt hơn là `wr`, công năng bảo toàn dữ liệu hoàn toàn giống hệt lệnh copy ở trên).*

### 2. Nhóm lệnh xóa sạch cấu hình (Đưa thiết bị về trạng thái xuất xưởng)

* **Bước 1:** Thực hiện xóa tệp khởi động trong bộ nhớ NVRAM:

```ios
Router# erase startup-config
```

* **Bước 2:** Ấn `Enter` xác nhận, sau đó gõ lệnh khởi động lại thiết bị:

```ios
Router# reload
```

*(Sau khi reboot xong, thiết bị sẽ hoàn toàn sạch trắng cấu hình ban đầu).*

---

## IV. Xử lý lỗi "Bẫy đề bài" và Check lỗi bài Lab tự động chấm điểm `.pka`

### 1. Khái niệm bài Lab tự động `.pka` (Packet Tracer Activity)

* Đề bài gồm 2 cửa sổ chạy song song: Sơ đồ mạng hạ tầng (**Topology**) và Bảng hướng dẫn (**Instructions**).
* Khi gõ đúng cấu hình theo yêu cầu, thanh tiến độ điểm số (**Completion**) ở góc dưới sẽ tự động nhảy tăng dần từ 0% lên mốc trọn vẹn 100%.
* **Mẹo ghim đề bài:** Click chọn nút **`Dock`** ở góc cửa sổ Instructions, đề bài sẽ tự động ghim chặt vào góc bên phải của giao diện giúp không bị ẩn khuất khi làm Lab.

### 2. Kỹ năng dò tìm lỗi Incorrect bằng tính năng "Check Results"

Khi cấu hình bị thiếu hoặc thanh tiến độ bị kẹt không lên đủ 100% điểm, thực hiện quy trình dò lỗi sau:

```text
               [ Điểm số bị thiếu hoặc kẹt phần trăm ]
                                 │
                                 ▼
                    [ Click nút CHECK RESULTS ]
             (Ở cạnh ô điểm số góc dưới cùng giao diện)
                                 │
                                 ▼
               [ Chọn tab ASSESSMENT ITEMS (Tab ở giữa) ]
                                 │
                                 ▼
       ► HỆ THỐNG HIỂN THỊ CHI TIẾT MA TRẬN LỖI:
       - Dấu TÍCH VÀNG XANH (✓): Lệnh đã gõ đúng chuẩn.
       - Dấu GẠCH CHÉO ĐỎ (X): Thông số gõ sai chính tả hoặc thiếu sót!
                                 │
                                 ▼
      [ Sửa lỗi: Nhìn đúng vị trí báo đỏ, gõ đè lại câu lệnh ]
              ➔ Thanh tiến độ tự động nhảy lên 100%

```

### 3. Lưu ý các bẫy đề bài thường gặp

* **Bẫy chữ hoa/chữ thường:** File chấm điểm tự động của Cisco cực kỳ nhạy cảm với ký tự hoa-thường. Nếu đề bài yêu cầu đặt mật khẩu là `cisco` (chữ c thường) nhưng học viên gõ thành `Cisco` (chữ C hoa), hệ thống sẽ chấm `Incorrect` và trừ điểm ngay lập tức.
* **Banner MOTD nhiều dòng:** Khi nhập biểu ngữ dài hoặc xuống dòng, bắt buộc phải sử dụng ký tự đóng mở đồng bộ:

```ios
  Router(config)# banner motd #
  Enter TEXT message. End with the character '#'.
  [Gõ nội dung dòng 1 theo đúng ký tự đề bài]
  [Gõ nội dung dòng 2 theo đúng ký tự đề bài]
  #

```

---

## V. Bài Tập Thực Hành Bắt Buộc (Day 4 Lớp)

* **Nhiệm vụ:** Hoàn thiện trọn vẹn 3 file bài Lab tự động chấm điểm: **Lab 2.5.5**, **Lab 2.7.6**, và **Lab 2.8.1**.
