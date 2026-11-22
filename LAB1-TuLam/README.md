# LAB 10 – MÃ HÓA ĐỐI XỨNG CỔ ĐIỂN

## 1. THÔNG TIN SINH VIÊN

Họ và tên: Triệu Ngọc Hào  
MSSV: 1150080050  
Lớp: 11DHCNPM1  
Tên bài thực hành: LAB 10 – Mã hóa đối xứng cổ điển  
Tên thư mục bài nộp: LAB1-TuLam

## 2. PHIÊN BẢN VÀ MÔI TRƯỜNG THỰC HIỆN

Hệ điều hành:
- Windows 10 64-bit.

Môi trường lập trình:
- Apache NetBeans IDE 32.
- Java JDK 23 (môi trường chạy Maven).
- Java Compiler Release: 17.
- Apache Maven.
- Java Swing và NetBeans GUI Builder.

Hệ quản trị cơ sở dữ liệu:
- Microsoft SQL Server Express.
- SQL Server Management Studio (SSMS).
- SQL Server Instance: SQLEXPRESS01.
- Server: 127.0.0.1.
- TCP Port: 14333.
- Database: LAB1_MaHoaCoDien.
- SQL Login: lab1_user.

Thông tin project:
- Project Name: LAB1_MaHoaCoDien.
- Group ID: vn.edu.lab.
- Package: week_01.
- Project Type: Java Application with Maven.
- Project Location: D:\BaoMat\LAB1_MaHoaCoDien.

Thư viện sử dụng:
- Microsoft JDBC Driver for SQL Server.
- mssql-jdbc phiên bản 12.8.1.jre11.
- Java Swing.
- Java SQL API.

Các thuật toán thực hành:
1. Caesar Cipher.
2. Vigenère Cipher.
3. Rail Fence Cipher.
4. Playfair Cipher.
5. Columnar Transposition Cipher.
6. Autokey Cipher.

## 3. CÁCH DỰNG MÔI TRƯỜNG

- Cài đặt Java JDK và kiểm tra bằng lệnh java -version.
- Tải Apache NetBeans IDE 32 và giải nén.
- Khởi động NetBeans và tạo Java Application bằng Maven.
- Đặt tên project LAB1_MaHoaCoDien.
- Thiết lập package week_01.
- Cấu hình file pom.xml.
- Khai báo thư viện mssql-jdbc.
- Thiết lập maven.compiler.release = 17.
- Sử dụng Java Swing GUI Builder để thiết kế giao diện.
- Tạo các lớp xử lý thuật toán mã hóa và giải mã.
- Tạo lớp FileTextIO hỗ trợ đọc và lưu tệp văn bản.
- Xây dựng chương trình tổng hợp sáu thuật toán.
- Tạo các lớp DbConfig và HistoryDao để kết nối, ghi và đọc lịch sử SQL Server.
- Cài đặt Microsoft SQL Server Express và SSMS.
- Kiểm tra các dịch vụ SQL Server trong Windows Services.
- Cấu hình TCP/IP và cổng 14333.
- Cấu hình SQL Server Authentication.
- Chuẩn bị database LAB1_MaHoaCoDien.
- Chuẩn bị bảng dbo.history để lưu lịch sử thao tác.
- Xây dựng giao diện kết nối SQL Server và xem lịch sử.

Các lớp chính của project:

- CaesarCipher.java
- VigenereCipher.java
- RailFenceCipher.java
- PlayfairCipher.java
- TranspositionCipher.java
- AutokeyCipher.java
- CipherTongHop.java
- FileTextIO.java
- DbConfig.java
- HistoryDao.java

Các form giao diện:

- frm_Caesar.java
- frm_Vigenere.java
- frm_RailFence.java
- frm_Playfair.java
- frm_Transposition.java
- frm_Autokey.java
- frm_TongHop.java
- frm_KetNoiSQL.java
- frm_LichSu.java
- frm_TanCong.java
- frm_Menu.java

## 4. CÁC TÌNH HUỐNG THỰC HÀNH

### Tình huống 1: Mã hóa Caesar

Mục tiêu:
- Thực hiện mã hóa và giải mã Caesar Cipher.
- Sử dụng khóa số nguyên k.
- Giữ nguyên các ký tự không thuộc bảng chữ cái.
- Kiểm tra tính chính xác của kết quả mã hóa và giải mã.

Công thức:

- Mã hóa: C = (P + k) mod 26.
- Giải mã: P = (C - k) mod 26.

Dữ liệu kiểm thử:
- Plaintext: HELLO.
- Key: 3.
- Ciphertext mong đợi: KHOOR.

Các chức năng:
- Encrypt.
- Decrypt.
- Open Ciphertext File.
- Save Ciphertext File.
- Kiểm tra khóa không hợp lệ.

### Tình huống 2: Mã hóa Vigenère

Mục tiêu:
- Sử dụng khóa gồm các ký tự A-Z.
- Lặp lại khóa tương ứng với độ dài bản rõ.
- Thực hiện mã hóa và giải mã theo từng ký tự.
- Kiểm tra xử lý khóa không hợp lệ.

Công thức:

- Mã hóa: Cᵢ = (Pᵢ + Kᵢ) mod 26.
- Giải mã: Pᵢ = (Cᵢ - Kᵢ) mod 26.

Dữ liệu kiểm thử:
- Plaintext: HELLO.
- Key: KEY.
- Ciphertext mong đợi: RIJVS.

### Tình huống 3: Mã hóa Rail Fence

Mục tiêu:
- Sắp xếp ký tự theo đường zíc-zắc.
- Sử dụng số hàng làm khóa.
- Đọc lần lượt các hàng để tạo bản mã.
- Khôi phục bản rõ từ bản mã.

Dữ liệu kiểm thử:
- Plaintext: HELLO WORLD.
- Key: 3.
- Ciphertext mong đợi: HOREL OLLWD.

### Tình huống 4: Mã hóa Playfair

Mục tiêu:
- Xây dựng bảng khóa Playfair 5x5.
- Chia bản rõ thành các cặp ký tự.
- Thực hiện mã hóa theo quy tắc cùng hàng, cùng cột và hình chữ nhật.
- Giải mã để khôi phục nội dung.

Dữ liệu kiểm thử:
- Plaintext: HELLOWORLD.
- Key: KEYWORD.
- Ciphertext mong đợi: GYIZSCOKCFBU.

### Tình huống 5: Mã hóa hoán vị cột

Mục tiêu:
- Sắp xếp văn bản thành các cột.
- Sử dụng khóa hoán vị để xác định thứ tự cột.
- Thực hiện mã hóa bằng cách thay đổi vị trí ký tự.
- Giải mã theo thứ tự hoán vị ngược.

Dữ liệu kiểm thử:
- Plaintext: BAOMAT.
- Key: 2,3,1.
- Ciphertext mong đợi: AAOTBM.

### Tình huống 6: Mã hóa Autokey

Mục tiêu:
- Tìm hiểu phương pháp mã hóa sử dụng khóa tự động mở rộng.
- Thực hiện mã hóa và giải mã Autokey.
- Kiểm tra kết quả với dữ liệu mẫu.

Dữ liệu kiểm thử:
- Plaintext: HELLO.
- Key: KEY.
- Ciphertext mong đợi: RIJSS.

### Tình huống 7: Chương trình tổng hợp sáu thuật toán

Mục tiêu:
- Tích hợp sáu thuật toán vào cùng một giao diện.
- Chọn thuật toán bằng JComboBox.
- Tự động hiển thị khóa mẫu phù hợp.
- Thực hiện mã hóa và giải mã.
- Đọc và lưu bản mã từ tệp văn bản.
- Kết nối SQL Server để lưu lịch sử thao tác.

Các thành phần chính:
- CipherTongHop.java.
- frm_TongHop.java.
- FileTextIO.java.
- HistoryDao.java.

Các nút chức năng:
- Encrypt.
- Decrypt.
- Open File.
- Save File.
- Kết nối SQL Server.
- Xem lịch sử.

Đã chạy được giao diện chương trình tổng hợp và mở form kết nối SQL Server.

### Tình huống 8: Kết nối Microsoft SQL Server

Mục tiêu:
- Kết nối ứng dụng Java với Microsoft SQL Server.
- Sử dụng JDBC Driver.
- Tạo database LAB1_MaHoaCoDien.
- Lưu lịch sử mã hóa và giải mã vào bảng dbo.history.
- Truy vấn và thống kê lịch sử.

Các cột của bảng dbo.history:
- id.
- created_at.
- algorithm.
- operation.
- input_text.
- result_text.

Các thao tác:
- Test Connection.
- INSERT dữ liệu lịch sử.
- SELECT dữ liệu lịch sử.
- Hiển thị 50 bản ghi mới nhất.
- Thống kê số lần thực hiện theo thuật toán.

Đã cấu hình và kiểm tra SQL Server trên máy cá nhân. Trong quá trình chạy ứng dụng, đã gặp lỗi đăng nhập và truy cập database cần tiếp tục xác nhận sau khi sửa quyền.

### Tình huống 9: Phân tích mật mã cổ điển

Mục tiêu:
- Tìm hiểu những điểm yếu của thuật toán mã hóa cổ điển.
- Phân tích khả năng khôi phục bản rõ hoặc khóa.
- Thực hiện các kỹ thuật phân tích trong phạm vi bài lab.

Các kỹ thuật:
1. Caesar Brute Force.
2. Frequency Analysis.
3. Known Plaintext Attack.

Ví dụ:
- Caesar Brute Force: thử các khóa để khôi phục HELLO từ KHOOR.
- Frequency Analysis: thống kê tần suất xuất hiện của từng ký tự.
- Known Plaintext: phân tích cặp bản rõ và bản mã để suy ra khóa tương ứng.

## 5. KẾT QUẢ PASS/FAIL

### Kịch bản 1 – Chuẩn bị môi trường

- Cài đặt Apache NetBeans: PASS.
- Tạo Java Maven Project: PASS.
- Cấu hình pom.xml: PASS sau khi sửa lỗi.
- Khắc phục lỗi XML thiếu thẻ đóng: PASS.
- Biên dịch các file Java sau khi sửa lỗi cú pháp: Cần xác nhận kết quả Build cuối cùng.
- Kết quả: Đã thiết lập môi trường và chạy được giao diện ứng dụng.

### Kịch bản 2 – Các thuật toán mã hóa

- Tạo các lớp thuật toán: Đã chuẩn bị.
- Kiểm thử Caesar: Chưa có log xác nhận.
- Kiểm thử Vigenère: Chưa có log xác nhận.
- Kiểm thử Rail Fence: Chưa có log xác nhận.
- Kiểm thử Playfair: Chưa có log xác nhận.
- Kiểm thử Transposition: Chưa có log xác nhận.
- Kiểm thử Autokey: Chưa có log xác nhận.
- Kết quả: Chưa xác nhận toàn bộ bộ kiểm thử.

### Kịch bản 3 – Chương trình tổng hợp

- Tạo frm_TongHop: PASS.
- Hiển thị giao diện tổng hợp: PASS.
- Tạo các nút Encrypt và Decrypt: PASS về phần mã xử lý.
- Tạo nút Open và Save: PASS về phần mã xử lý.
- Mở giao diện kết nối SQL Server: PASS.
- Mở giao diện lịch sử: Đã viết mã, chưa xác nhận kết quả tải dữ liệu.
- Kết quả: Giao diện tổng hợp đã chạy được.

### Kịch bản 4 – SQL Server

- Cài đặt SQL Server: PASS.
- Mở SQL Server Management Studio: PASS.
- Xác định instance SQLEXPRESS01: PASS.
- Kiểm tra cấu hình TCP/IP: PASS.
- Thiết lập cổng 14333: Đã cấu hình.
- Kiểm tra Windows Authentication: PASS trên instance SQLEXPRESS01.
- Kiểm tra chế độ Mixed Mode: PASS, đã xác nhận WindowsOnly = 0.
- Kết nối từ Java tới database: FAIL tại lần kiểm thử đã ghi nhận.
- Thông báo lỗi: Cannot open database requested by the login.
- Kết quả: Chưa xác nhận kết nối hoàn chỉnh.

### Kịch bản 5 – Lưu và đọc lịch sử

- Tạo frm_LichSu: PASS về phần mã giao diện.
- Viết phương thức loadData(): PASS sau khi sửa cú pháp.
- Tạo nút Tải lại: PASS về phần mã xử lý.
- Lưu thao tác vào SQL Server: Chưa xác nhận.
- Hiển thị lịch sử từ dbo.history: Chưa xác nhận.
- Truy vấn lịch sử bằng SSMS: Chưa có kết quả kiểm thử.
- Kết quả: Chưa hoàn tất kiểm thử.

### Kịch bản 6 – Phân tích mật mã

- Brute Force Caesar: Chưa xác nhận.
- Frequency Analysis: Chưa xác nhận.
- Known Plaintext Attack: Chưa xác nhận.
- Kết quả: Chưa có log kiểm thử.

## 6. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC

### Lỗi 1: Maven không đọc được pom.xml

Thông báo lỗi:
- Non-readable POM.
- Expected end tag project.

Nguyên nhân:
- Cấu trúc XML không hợp lệ.
- Thiếu ký tự đóng của thẻ project.

Cách khắc phục:
- Mở pom.xml trong NetBeans.
- Kiểm tra toàn bộ cấu trúc XML.
- Bổ sung thẻ đóng `</project>`.
- Lưu file và chạy Clean and Build.

### Lỗi 2: Không thể kết nối SQL Server

Thông báo lỗi:
- Login failed for user lab1_user.
- Microsoft SQL Server Error 18456.

Nguyên nhân có thể:
- Tài khoản SQL chưa được tạo đúng instance.
- Chưa bật SQL Server Authentication.
- Tài khoản chưa được cấp quyền hoặc mật khẩu không hợp lệ.

Cách khắc phục:
- Kiểm tra dịch vụ SQL Server.
- Xác định instance đang hoạt động.
- Kiểm tra SQL Server Authentication.
- Kiểm tra tài khoản và quyền truy cập database.

### Lỗi 3: Kết nối sai SQL Server Instance

Thông báo lỗi:
- Instance failure.
- Failed to connect to server SQLEXPRESS.

Nguyên nhân:
- Máy có hai instance SQLEXPRESS và SQLEXPRESS01.
- SQLEXPRESS không chạy.
- SQLEXPRESS01 đang hoạt động.

Cách khắc phục:
- Mở Windows Services.
- Kiểm tra trạng thái các dịch vụ SQL Server.
- Sử dụng đúng instance localhost\SQLEXPRESS01.
- Tránh thao tác trên instance không hoạt động.

### Lỗi 4: Windows Authentication bị từ chối

Thông báo lỗi:
- Login is from an untrusted domain.
- Microsoft SQL Server Error 18452.

Nguyên nhân có thể:
- Sự cố xác thực tài khoản Windows.
- Phương thức kết nối chưa phù hợp.

Cách khắc phục:
- Kiểm tra kết nối bằng tên instance.
- Kiểm tra chế độ xác thực SQL Server.
- Sử dụng SQL Server Authentication với tài khoản được cấu hình cho bài lab.

### Lỗi 5: SQL Server chưa bật Mixed Mode

Kết quả truy vấn ban đầu:
- InstanceName: SQLEXPRESS01.
- WindowsOnly: 1.

Nguyên nhân:
- SQL Server chỉ cho phép Windows Authentication.

Cách khắc phục:
- Mở Server Properties.
- Chọn Security.
- Bật SQL Server and Windows Authentication mode.
- Khởi động lại dịch vụ SQL Server.
- Kiểm tra lại WindowsOnly = 0.

### Lỗi 6: Lỗi biên dịch frm_LichSu.java

Thông báo lỗi:
- ';' expected.
- illegal start of expression.
- BUILD FAILURE.

Nguyên nhân:
- Thiếu dấu ngoặc nhọn đóng phương thức loadData().
- Hàm initComponents() bị trình biên dịch hiểu sai vị trí.

Cách khắc phục:
- Mở frm_LichSu.java.
- Kiểm tra phương thức loadData().
- Bổ sung dấu ngoặc nhọn đóng hàm.
- Lưu mã nguồn và biên dịch lại.

### Lỗi 7: Không mở được database từ ứng dụng Java

Thông báo lỗi:
- Cannot open database LAB1_MaHoaCoDien requested by the login.
- The login failed.

Nguyên nhân có thể:
- Database chưa tồn tại trên instance đang kết nối.
- SQL Login chưa được ánh xạ thành Database User.
- Tài khoản thiếu quyền truy cập database.

Hướng khắc phục:
- Kiểm tra database trên SQL Server.
- Kiểm tra trạng thái ONLINE.
- Kiểm tra ánh xạ login và user.
- Cấp quyền CONNECT, SELECT và INSERT phù hợp.
- Kiểm thử lại ứng dụng Java.

## 7. LOG KIỂM TRA

Các công cụ và log sử dụng:

- NetBeans Output: kết quả biên dịch Maven.
- Maven Clean and Build.
- Java Compiler: kiểm tra lỗi cú pháp.
- NetBeans GUI Builder: kiểm tra form giao diện.
- SQL Server Configuration Manager.
- Windows Services.
- SQL Server Management Studio.
- SQL Query Results.
- Java JDBC Connection Test.

Các truy vấn SQL kiểm tra:

```sql
SELECT
    SERVERPROPERTY('ServerName') AS ServerName,
    SERVERPROPERTY('InstanceName') AS InstanceName,
    SERVERPROPERTY('IsIntegratedSecurityOnly') AS WindowsOnly;
```

```sql
USE LAB1_MaHoaCoDien;

SELECT TOP (20)
    id,
    created_at,
    algorithm,
    operation,
    input_text,
    result_text
FROM dbo.history
ORDER BY id DESC;
```

```sql
SELECT
    algorithm,
    COUNT(*) AS so_lan
FROM dbo.history
GROUP BY algorithm
ORDER BY so_lan DESC;
```

Các minh chứng đã có:
- Giao diện chương trình tổng hợp sáu thuật toán.
- Giao diện kết nối Microsoft SQL Server.
- Cấu hình cổng TCP 14333.
- Danh sách SQL Server Instance.
- Kết quả truy vấn chế độ xác thực SQL Server.
- Thông báo lỗi kết nối database.
- Log Maven và lỗi biên dịch frm_LichSu.java.

Các minh chứng cần bổ sung:
- Kết quả mã hóa và giải mã của sáu thuật toán.
- Kết quả Test Connection thành công.
- Dữ liệu thực tế trong bảng dbo.history.
- Giao diện xem lịch sử.
- Kết quả ba kỹ thuật phân tích mật mã.
- Kết quả Clean and Build cuối cùng.

Các tệp SQL phục vụ bài lab:
- 01_Tao_CSDL.sql.
- 03_Kiem_Tra_Du_Lieu.sql.

Không nộp script tạo tài khoản chứa mật khẩu SQL Server.

## 8. KẾT LUẬN

LAB 1 tập trung vào việc tìm hiểu và triển khai sáu thuật toán mã hóa đối xứng cổ điển bằng ngôn ngữ Java, kết hợp giao diện Java Swing và quản lý dữ liệu bằng Microsoft SQL Server.

Trong quá trình thực hành, đã thiết lập môi trường Apache NetBeans, tạo project Maven, xây dựng các lớp Java xử lý thuật toán và thiết kế giao diện chương trình tổng hợp.

Phần giao diện tổng hợp và kết nối SQL Server đã được triển khai. Quá trình thực hiện gặp một số lỗi liên quan đến cấu trúc pom.xml, cú pháp Java, SQL Server Instance và quyền truy cập database. Các lỗi đã được phân tích và thực hiện các bước khắc phục tương ứng.

Chương trình đã mở được giao diện tổng hợp, tuy nhiên phần lưu và truy vấn lịch sử SQL Server cùng một số bộ kiểm thử thuật toán vẫn cần được xác nhận đầy đủ.

Qua bài thực hành, hiểu rõ hơn nguyên lý mã hóa và giải mã cổ điển, cách xây dựng giao diện bằng Java Swing, sử dụng Maven để quản lý project và kết nối ứng dụng Java với SQL Server thông qua JDBC.

Bài lab cũng giúp rèn luyện kỹ năng phân tích lỗi biên dịch, cấu hình hệ quản trị cơ sở dữ liệu, xử lý lỗi kết nối và đánh giá mức độ an toàn của các thuật toán mật mã cổ điển.