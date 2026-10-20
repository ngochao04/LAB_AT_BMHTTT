# LAB 7 – XÂY DỰNG CHÍNH SÁCH WEB PROTECTION TRÊN MÔ HÌNH pfSense + WEB PROXY

## 1. THÔNG TIN SINH VIÊN

Họ và tên: Triệu Ngọc Hào  
MSSV: 1150080050  
Lớp: 11DHCNPM1  
Tên bài thực hành: LAB 7 – Xây dựng chính sách Web Protection trên mô hình pfSense + Web Proxy

## 2. PHIÊN BẢN VÀ MÔI TRƯỜNG THỰC HIỆN

Phần mềm ảo hóa:

- VMware Workstation

Firewall/Gateway:

- pfSense CE

Máy chủ Domain Controller:

- Windows Server 2019/2022
- Active Directory Domain Services
- DNS Server
- DC01: 10.6.0.10/24
- Gateway: 10.6.0.1
- DNS: 10.6.0.10

Máy Web Proxy:

- Ubuntu Server 24.04 LTS
- RAM: 2 GB
- CPU: 2 vCPU
- Disk: 20 GB
- IP: 10.6.0.30/24
- Gateway: 10.6.0.1
- DNS: 10.6.0.10
- E2Guardian: 10.6.0.30:8080
- Squid: 127.0.0.1:3128
- ClamAV: chạy local trên WEB-PROXY

Máy trạm:

- Windows 10
- Client-A: 10.6.0.21/24
- Client-B: 10.6.0.22/24
- Gateway: 10.6.0.1
- DNS: 10.6.0.10

Máy chủ DMZ:

- Ubuntu Server
- Nginx Web Server
- DMZ-WEB: 172.16.6.10/24
- Gateway: 172.16.6.1

Mô hình mạng:

- WAN: VMware NAT
- MGMT: 192.168.56.0/24
- LAN: 10.6.0.0/24
- DMZ: 172.16.6.0/24

Địa chỉ pfSense:

- WAN: DHCP
- MGMT: 192.168.56.254/24
- LAN: 10.6.0.1/24
- DMZ: 172.16.6.1/24

Các website mô phỏng LAB 7:

- normal.lab7.test
- china.lab7.test
- mideast.lab7.test
- jobs.lab7.test
- weapons.lab7.test
- politics.lab7.test
- religion.lab7.test
- education.lab7.test
- entertainment.lab7.test
- games.lab7.test
- streaming.lab7.test
- blockedgame.lab7.test
- download.lab7.test

Tất cả các domain lab7.test trỏ về DMZ-WEB 172.16.6.10.

## 3. CÁCH DỰNG MÔI TRƯỜNG

- Sử dụng VMware Workstation để chạy các máy ảo trong mô hình LAB.
- pfSense được cấu hình làm firewall/gateway cho các mạng WAN, MGMT, LAN và DMZ.
- Windows Server làm Domain Controller và DNS Server.
- Client-A và Client-B sử dụng Windows 10 trong mạng LAN.
- Ubuntu Server 24.04 LTS được dùng làm WEB-PROXY, cài Squid, E2Guardian và ClamAV.
- DMZ-WEB chạy Nginx và chứa các website mô phỏng của LAB 7.
- Trên DC01 tạo DNS cho các domain `lab7.test` trỏ về DMZ-WEB.
- Trình duyệt Client-A/B được cấu hình đi qua proxy `10.6.0.30:8080`.
- Trên pfSense tạo Alias và firewall rule để ép lưu lượng Web qua WEB-PROXY và chặn truy cập trực tiếp HTTP/HTTPS.
- Trên E2Guardian cấu hình các chính sách block, warning, whitelist và kiểm thử antivirus/download.

## 4. CÁC TÌNH HUỐNG ĐÃ THỰC HIỆN

### Tình huống 1: Baseline – website bình thường

- Truy cập normal.lab7.test khi bật proxy.
- Website truy cập bình thường.
- Tắt proxy và thử truy cập trực tiếp HTTP/HTTPS.
- pfSense chặn kết nối trực tiếp từ Client-A/B.

### Tình huống 2: Geo-Sim

- Truy cập china.lab7.test.
- Truy cập mideast.lab7.test.
- Hai website bị hard block.
- Không cho phép temporary bypass.

### Tình huống 3: Job Search

- Truy cập jobs.lab7.test.
- Website bị hard block.

### Tình huống 4: Warning Content

- Truy cập weapons.lab7.test.
- Truy cập politics.lab7.test.
- Truy cập religion.lab7.test.
- Xuất hiện trang cảnh báo.
- Người dùng có thể thực hiện temporary bypass trong thời gian ngắn.

### Tình huống 5: Education

- Truy cập education.lab7.test.
- Website được phép truy cập.
- Không xuất hiện cảnh báo.

### Tình huống 6: Entertainment/Game theo Schedule

- Truy cập entertainment.lab7.test và games.lab7.test.
- Trong khung LAB7_ENT_60M: ALLOW.
- Ngoài khung thời gian: BLOCK.

### Tình huống 7: Streaming

- Truy cập streaming.lab7.test.
- Hiển thị cảnh báo.
- Cho phép temporary bypass trong thời gian ngắn.

### Tình huống 8: Specific Game

- Truy cập blockedgame.lab7.test.
- Website bị hard block.

### Tình huống 9: Antivirus

- Truy cập download.lab7.test.
- Tải file eicar.com.txt.
- ClamAV/E2Guardian phát hiện và chặn hoặc cảnh báo file kiểm thử.

### Tình huống 10: Giới hạn kích thước download

- Kiểm thử 100MB.bin.
- Kiểm thử 110MB.bin.
- Nếu strict size ACL hoạt động thì file 110MB phải bị từ chối theo policy.

## 5. KẾT QUẢ PASS/FAIL

Kịch bản 1 – Baseline:

- normal.lab7.test qua proxy: PASS.
- Tắt proxy, direct HTTP/HTTPS bị chặn: PASS.
- Kết quả: PASS.

Kịch bản 2 – Geo-Sim:

- china.lab7.test: BLOCK.
- mideast.lab7.test: BLOCK.
- Không có temporary bypass.
- Kết quả: PASS.

Kịch bản 3 – Job Search:

- jobs.lab7.test: BLOCK.
- Kết quả: PASS.

Kịch bản 4 – Warning Content:

- Hiển thị trang warning: PASS.
- Temporary bypass hoạt động trong thời gian ngắn: PASS.
- Kết quả: PASS.

Kịch bản 5 – Education:

- education.lab7.test truy cập bình thường.
- Không cảnh báo.
- Kết quả: PASS.

Kịch bản 6 – Entertainment/Game:

- Trong LAB7_ENT_60M: ALLOW.
- Ngoài schedule: BLOCK.
- Kết quả: PASS.

Kịch bản 7 – Streaming:

- Hiển thị warning.
- Temporary bypass hoạt động.
- Kết quả: PASS.

Kịch bản 8 – Specific Game:

- blockedgame.lab7.test: HARD BLOCK.
- Kết quả: PASS.

Kịch bản 9 – Antivirus:

- EICAR bị ClamAV/E2Guardian phát hiện.
- File bị chặn hoặc cảnh báo.
- Kết quả: PASS.

Kịch bản 10 – Size:

- 100MB.bin: xử lý theo cấu hình.
- 110MB.bin: bị từ chối nếu strict size ACL được hỗ trợ và bật.
- Kết quả: PASS nếu 110MB bị chặn đúng yêu cầu; nếu không thì ghi FAIL/NOT SUPPORTED theo môi trường thực tế.

## 6. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC

### Lỗi 1: Client không vào được bất kỳ website nào

Nguyên nhân:

- Trình duyệt cấu hình sai proxy.
- Client không ping được WEB-PROXY.
- E2Guardian hoặc Squid chưa chạy.
- Sai DNS hoặc firewall rule.

Cách khắc phục:

- Kiểm tra proxy 10.6.0.30:8080.
- Ping 10.6.0.30.
- Kiểm tra trạng thái E2Guardian và Squid.
- Kiểm tra DNS 10.6.0.10.
- Kiểm tra rule trên pfSense.

### Lỗi 2: Tắt proxy nhưng Client vẫn truy cập Web được

Nguyên nhân:

- Rule Block LAB7_CLIENTS -> Any WEB_PORTS đặt sai thứ tự.
- Chưa Apply Changes.
- State cũ của kết nối vẫn còn.

Cách khắc phục:

- Đưa rule Block direct 80/443 lên đúng vị trí.
- Apply Changes.
- Vào Diagnostics > States và Reset States.

### Lỗi 3: Proxy báo 502 hoặc Connection Failed

Nguyên nhân:

- Squid không hoạt động.
- WEB-PROXY không phân giải được DNS.
- Sai Gateway hoặc route.

Cách khắc phục:

- Kiểm tra Squid service.
- Kiểm tra DNS 10.6.0.10.
- Kiểm tra Gateway 10.6.0.1.
- Kiểm tra kết nối từ WEB-PROXY đến website đích.

### Lỗi 4: Website cần block nhưng vẫn truy cập được

Nguyên nhân:

- Domain chưa nằm đúng danh sách block.
- E2Guardian chưa reload/restart.
- Domain nằm nhầm trong exception list.
- Browser còn cache.

Cách khắc phục:

- Kiểm tra lại FQDN trong danh sách policy.
- Restart E2Guardian.
- Kiểm tra exceptionsitelist.
- Xóa cache hoặc mở lại trình duyệt.

### Lỗi 5: Không xuất hiện trang Warning/Temporary Bypass

Nguyên nhân:

- Chưa cấu hình bypass.
- reportinglevel chưa đúng.
- Website nằm trong non-bypass list.

Cách khắc phục:

- Kiểm tra bypass = 300.
- Kiểm tra reportinglevel.
- Xóa website khỏi non-bypass list nếu cần warning/bypass.

### Lỗi 6: EICAR vẫn tải được

Nguyên nhân:

- clamav-daemon chưa chạy.
- E2Guardian chưa bật content scanner.
- File nằm trong scan exception.
- HTTPS chưa được MITM nên không đọc được nội dung.

Cách khắc phục:

- Kiểm tra clamav-daemon.
- Bật clamd scanner trong E2Guardian.
- Kiểm tra danh sách exception.
- Với HTTPS, chỉ kiểm thử nội dung khi SSL MITM đã được cấu hình trong LAB.

### Lỗi 7: HTTPS báo lỗi chứng chỉ

Nguyên nhân:

- CA LAB chưa được import.
- Cấu hình SSL MITM chưa đúng.
- Trình duyệt còn cache chứng chỉ.

Cách khắc phục:

- Import CA LAB vào Trusted Root Certification Authorities trên Client-A/B.
- Kiểm tra cấu hình certificate/key của E2Guardian.
- Đóng và mở lại trình duyệt.

### Lỗi 8: File 100MB và 110MB đều tải được

Nguyên nhân:

- Squid build không hỗ trợ hoặc chưa bật strict size ACL.
- Chỉ cấu hình giới hạn scan/cache chứ chưa phải hard download-size limit.

Cách khắc phục:

- Kiểm tra cấu hình reply_body_max_size.
- Kiểm tra log Squid.
- Không kết luận policy PASS nếu 110MB vẫn tải được.

## 7. LOG KIỂM TRA

Các log được sử dụng:

- pfSense: Status > System Logs > Firewall.
- E2Guardian: /var/log/e2guardian/access.log.
- Squid: /var/log/squid/access.log.
- ClamAV: journalctl -u clamav-daemon hoặc log tương ứng.
- Client: lỗi trình duyệt, cấu hình proxy và kết quả phân giải DNS.

Sau mỗi kịch bản, đối chiếu log để xác định traffic đã bị firewall chặn, được proxy xử lý hay bị E2Guardian/ClamAV chặn.

## 8. KẾT LUẬN

Qua LAB 7, đã xây dựng mô hình Web Protection trong đó pfSense đảm nhiệm vai trò firewall/gateway và WEB-PROXY Ubuntu đảm nhiệm chức năng lọc nội dung Web.

Bài thực hành đã triển khai được cơ chế ép Client-A/B đi qua E2Guardian, chặn truy cập Web trực tiếp để chống bypass, xây dựng các chính sách Hard Block, Warning/Temporary Bypass, Exception/Whitelist, Schedule theo thời gian, quét file tải xuống bằng ClamAV và kiểm thử giới hạn kích thước file.

Qua đó hiểu rõ sự khác nhau giữa firewall lớp mạng/truyền tải và Web Proxy lớp ứng dụng, nguyên lý stateful firewall, cơ chế chống bypass, cách kiểm tra log trên nhiều tầng và các rủi ro khi triển khai HTTPS Inspection.
