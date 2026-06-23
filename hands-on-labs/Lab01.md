# Lab 01: Khởi Tạo Thiết Bị & Thay Đổi Hostname Trên Cisco IOS

## 1. Yêu cầu bài thực hành (Kịch bản Lab)
* **Công cụ giả lập:** Cisco Packet Tracer.
* **Thiết bị triển khai:** Router dòng **2811**.
* **Nhiệm vụ:**
  1. Khởi động Router lần đầu, gõ `no` để bỏ qua trình cấu hình tự động (`initial configuration dialog`).
  2. Sử dụng các câu lệnh CLI để di chuyển vào chế độ cấu hình toàn cục (`Global Configuration Mode`).
  3. Đổi tên Router mặc định thành tên viết tắt của học viên: **TRANQUY**.
  4. Sử dụng lệnh điều hướng để lùi thiết bị về lại chế độ người dùng ngoài cùng (**User EXEC Mode** - nhận diện bằng dấu `>`).
  5. Chụp màn hình minh chứng kết quả hiển thị tên mới kèm dấu `>`.

---

## 2. Các câu lệnh CLI đã thực thi

Dưới đây là toàn bộ quá trình gõ lệnh trên terminal của Router 2811:

```ios
--- Bước 1: Bỏ qua Configuration Dialog ban đầu ---
Would you like to enter the initial configuration dialog? [yes/no]: no

Press RETURN to get started!

--- Bước 2: Di chuyển từ User EXEC Mode vào Global Configuration Mode ---
Router> en
Router# conf t
Enter configuration commands, one per line. End with CNTL/Z.

--- Bước 3: Thực hiện thay đổi tên thiết bị (Hostname) ---
Router(config)# hostname TRANQUY

--- Bước 4: Lùi thiết bị về lại User EXEC Mode (Dấu >) ---
TRANQUY(config)# ^Z
TRANQUY#
%SYS-5-CONFIG_I: Configured from console by console

TRANQUY# exit
TRANQUY con0 is now available

Press RETURN to get started.
