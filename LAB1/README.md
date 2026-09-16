# BÁO CÁO THỰC HÀNH LAB

## 1. THÔNG TIN SINH VIÊN
- **Họ và tên:** Triệu Ngọc Hào
- **Mã số sinh viên:** 1150080050
- **Lớp / Môn học:** An toàn thông tin

---

## 2. TÊN BÀI LAB
**Lab 1: Bắt và phân tích gói tin Telnet - SSH**

---

## 3. MÔI TRƯỜNG THỰC NGHIỆM
- **Mạng cô lập:** Switch ảo Host-only / VMnet (Dải mạng: `10.0.0.0/24`)
- **WinServer22_Server (10.0.0.1):** Máy chủ chạy dịch vụ Telnet Server và OpenSSH Server.
- **Ubuntu_Client (10.0.0.2):** Máy trạm dùng để kết nối điều khiển từ xa.
- **Ubuntu_Attacker (10.0.0.3):** Máy đóng vai trò kẻ nghe lén trên đường truyền, dùng Wireshark.

---

## 4. NỘI DUNG ĐÃ THỰC HIỆN
1. **Thiết lập kết nối mạng:** Cấu hình địa chỉ IP tĩnh cố định cho cả 3 máy và kiểm tra thông mạng bằng lệnh `ping`.
2. **Khởi tạo tài khoản kiểm thử:** Tạo user định danh sinh viên `trieungochao` (mật khẩu MSSV: `1150080050`) trên Windows Server.
3. **Thực nghiệm giao thức Telnet (Port 23):**
   - Khởi chạy dịch vụ Telnet Server lắng nghe trên cổng 23.
   - Bật Wireshark trên máy Attacker lọc gói tin cổng 23 (`tcp.port == 23`).
   - Dùng máy Client thực hiện phiên đăng nhập Telnet, thực thi các lệnh hệ thống (`whoami`, `dir`).
4. **Thực nghiệm giao thức SSH (Port 22):**
   - Cài đặt và kích hoạt dịch vụ OpenSSH Server trên Windows Server.
   - Đổi bộ lọc Wireshark sang cổng 22 (`tcp.port == 22`) trên máy Attacker.
   - Dùng máy Client kết nối SSH an toàn và thực thi lệnh tương tự.

---

## 5. KẾT QUẢ THỰC HIỆN
- **Với Telnet (Bắt gói trên Wireshark):**
  - Chức năng *Follow TCP Stream* hiển thị toàn bộ luồng dữ liệu dưới dạng văn bản thô (Cleartext).
  - Kẻ tấn công đọc trộm được hoàn toàn tên tài khoản (`trieungochao`), mật khẩu (`1150080050`) và toàn bộ nội dung lệnh được thực thi.
- **Với SSH (Bắt gói trên Wireshark):**
  - Sau giai đoạn bắt tay trao đổi khóa (Key Exchange), toàn bộ nội dung phiên làm việc đều là các gói tin mã hóa (`Encrypted packet payload`).
  - Không thể đọc được tài khoản, mật khẩu hay lệnh điều khiển, chứng minh tính bảo mật tuyệt đối về dữ liệu kênh truyền của SSH so với Telnet.
