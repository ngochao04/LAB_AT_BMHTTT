LAB 6 – XÁC THỰC ACTIVE DIRECTORY VÀ CHÍNH SÁCH TRUY CẬP TRÊN pfSense

1. THÔNG TIN SINH VIÊN

Họ và tên: Triệu Ngọc Hào
MSSV: 1160080050
Lớp: 11DHCNPM1
Tên bài thực hành: LAB 6 – Xác thực Active Directory và chính sách truy cập trên pfSense


2. PHIÊN BẢN VÀ MÔI TRƯỜNG THỰC HIỆN

Phần mềm ảo hóa:
- VMware Workstation

Firewall:
- pfSense CE

Máy chủ:
- Windows Server 2019/2022
- Active Directory Domain Services
- DNS Server
- Domain: lab6.test

Máy trạm:
- Windows 10
- Client-A: 10.6.0.21
- Client-B: 10.6.0.22

Máy chủ DMZ:
- Ubuntu Server
- Nginx Web Server

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

Domain Controller:
- DC01: 10.6.0.10/24
- Gateway: 10.6.0.1
- DNS: 10.6.0.10

DMZ-WEB:
- GENERAL_SITE: 172.16.6.10
- JOB_SITE: 172.16.6.20
- SOCIAL_SITE: 172.16.6.30
- RESTRICTED_SITE: 172.16.6.40


3. CÁCH DỰNG MÔI TRƯỜNG

Bước 1: Cấu hình mạng trên VMware
- VMnet8 dùng NAT cho WAN.
- VMnet1 dùng Host-only cho mạng MGMT 192.168.56.0/24.
- VMnet2 dùng Host-only cho mạng LAN 10.6.0.0/24.
- VMnet3 dùng Host-only cho mạng DMZ 172.16.6.0/24.
- Tắt DHCP VMware trên các mạng MGMT, LAN và DMZ để sử dụng địa chỉ IP tĩnh.

Bước 2: Cấu hình pfSense
- Gắn 4 Network Adapter tương ứng WAN, MGMT, LAN và DMZ.
- Cấu hình WAN nhận IP bằng DHCP.
- Cấu hình MGMT: 192.168.56.254/24.
- Cấu hình LAN: 10.6.0.1/24.
- Cấu hình DMZ: 172.16.6.1/24.
- Tắt DHCP Server trên LAN, MGMT và DMZ.

Bước 3: Cấu hình Domain Controller
- Cài Windows Server.
- Đặt địa chỉ IP 10.6.0.10/24.
- Gateway: 10.6.0.1.
- DNS: 10.6.0.10.
- Cài Active Directory Domain Services và DNS Server.
- Tạo forest mới: lab6.test.
- Tạo OU LAB6-Users.
- Tạo hai tài khoản sv_a và sv_b.
- Tạo service account svc_pfsense_ldap để pfSense sử dụng khi truy vấn LDAP.

Bước 4: Tích hợp LDAP với pfSense
- Vào System > User Manager > Authentication Servers.
- Tạo LDAP Server tên LAB6-AD.
- LDAP Server: 10.6.0.10.
- Port: 389.
- Base DN: DC=lab6,DC=test.
- Authentication Container: OU=LAB6-Users,DC=lab6,DC=test.
- User Naming Attribute: samAccountName.
- Kiểm thử tài khoản sv_a và sv_b bằng Diagnostics > Authentication.

Bước 5: Cấu hình Captive Portal
- Tạo zone LAB6_USERS.
- Chọn interface LAN.
- Authentication Method: Authentication backend.
- Authentication Server: LAB6-AD.
- Kiểm thử đăng nhập bằng sv_a và sv_b.

Bước 6: Tạo Alias
- CLIENT_A = 10.6.0.21.
- CLIENT_B = 10.6.0.22.
- POLICY_CLIENTS = 10.6.0.21, 10.6.0.22.
- GENERAL_SITE = 172.16.6.10.
- JOB_SITE = 172.16.6.20.
- SOCIAL_SITE = 172.16.6.30.
- RESTRICTED_SITE = 172.16.6.40.
- WEB_PORTS = 80, 443.

Bước 7: Tạo Schedule
- LUNCH_WINDOW: 11:30 – 13:00, từ Thứ 2 đến Thứ 6.
- WORK_HOURS: 08:00 – 17:00, từ Thứ 2 đến Thứ 6.
- TEST_NOW: khoảng thời gian ngắn dùng để kiểm thử schedule.


4. CÁC TÌNH HUỐNG ĐÃ THỰC HIỆN

Tình huống 1: Chặn Client-A truy cập JOB_SITE
- Source: CLIENT_A.
- Destination: JOB_SITE.
- Protocol: TCP.
- Port: 80.
- GENERAL_SITE vẫn được phép truy cập.

Tình huống 2: Chặn RESTRICTED_SITE cho Client-A và Client-B
- Source: POLICY_CLIENTS.
- Destination: RESTRICTED_SITE.
- Protocol: TCP/80.
- Cả Client-A và Client-B đều bị chặn.
- GENERAL_SITE vẫn được phép truy cập.

Tình huống 3: Client-B chỉ được truy cập SOCIAL_SITE trong giờ nghỉ
- Rule Pass CLIENT_B -> SOCIAL_SITE có Schedule LUNCH_WINDOW.
- Rule Block CLIENT_B -> SOCIAL_SITE đặt phía dưới.
- Trong schedule: cho phép.
- Ngoài schedule: bị chặn.

Tình huống 4: Chỉ cho HTTP/HTTPS trong giờ hành chính
- Source: POLICY_CLIENTS.
- Destination: Any.
- Port: WEB_PORTS (80, 443).
- Schedule: WORK_HOURS.
- Các dịch vụ khác bị chặn bởi rule Block bên dưới.

Tình huống 5: Cô lập DMZ khỏi LAN
- Block DMZ net -> LAN net.
- Cho phép LAN truy cập GENERAL_SITE khi policy cho phép.
- DMZ không được tự khởi tạo kết nối vào LAN.


5. KẾT QUẢ PASS/FAIL

LDAP sv_a:
- Kết quả dự kiến: PASS.
- Kết quả thực tế: PASS.

LDAP sv_b:
- Kết quả dự kiến: PASS.
- Kết quả thực tế: PASS.

Captive Portal:
- Kết quả dự kiến: User domain đăng nhập được.
- Kết quả thực tế: PASS.

Kịch bản 1 – JOB_SITE:
- Client-A -> GENERAL_SITE: PASS.
- Client-A -> JOB_SITE: BLOCK đúng yêu cầu.
- Kết quả: PASS.

Kịch bản 2 – RESTRICTED_SITE:
- Client-A -> RESTRICTED_SITE: BLOCK.
- Client-B -> RESTRICTED_SITE: BLOCK.
- GENERAL_SITE vẫn truy cập được.
- Kết quả: PASS.

Kịch bản 3 – SOCIAL_SITE:
- Trong LUNCH_WINDOW: ALLOW.
- Ngoài LUNCH_WINDOW: BLOCK.
- Kết quả: PASS.

Kịch bản 4 – WORK_HOURS:
- HTTP/HTTPS trong giờ hành chính: ALLOW.
- Các dịch vụ khác: BLOCK.
- Ngoài giờ hành chính: BLOCK theo rule phía dưới.
- Kết quả: PASS.

Kịch bản 5 – DMZ Isolation:
- LAN -> GENERAL_SITE: ALLOW.
- DMZ -> LAN: BLOCK.
- Kết quả: PASS.


6. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC

Lỗi 1: Không truy cập được WebGUI pfSense
Nguyên nhân:
- Sai card VMware.
- Sai IP MGMT.
- Host-only adapter chưa được cấu hình đúng.

Cách khắc phục:
- Kiểm tra lại VMnet1.
- Kiểm tra IP MGMT của pfSense là 192.168.56.254/24.
- Kiểm tra card mạng máy thật cùng subnet 192.168.56.0/24.

Lỗi 2: Client không kết nối được pfSense
Nguyên nhân:
- Client gắn sai VMnet.
- Sai địa chỉ IP hoặc Default Gateway.

Cách khắc phục:
- Đặt Client vào VMnet2.
- Client-A dùng 10.6.0.21/24.
- Client-B dùng 10.6.0.22/24.
- Gateway là 10.6.0.1.

Lỗi 3: LDAP Authentication thất bại
Nguyên nhân:
- Sai Base DN hoặc Authentication Container.
- Sai Bind DN hoặc password.
- pfSense không kết nối được DC01.
- Tài khoản service bị khóa.

Cách khắc phục:
- Kiểm tra Base DN: DC=lab6,DC=test.
- Kiểm tra OU: OU=LAB6-Users,DC=lab6,DC=test.
- Kiểm tra svc_pfsense_ldap.
- Kiểm tra kết nối tới 10.6.0.10 port 389.

Lỗi 4: Firewall rule thay đổi nhưng kết quả kiểm thử không thay đổi
Nguyên nhân:
- State cũ của kết nối vẫn còn trong State Table.

Cách khắc phục:
- Vào Diagnostics > States.
- Reset States.
- Tạo kết nối mới rồi kiểm thử lại.

Lỗi 5: Schedule không hoạt động đúng
Nguyên nhân:
- Sai múi giờ pfSense.
- Schedule không nằm trong thời điểm hiện tại.
- Không có rule Block bên dưới rule Pass có Schedule.

Cách khắc phục:
- Kiểm tra thời gian và timezone của pfSense.
- Dùng TEST_NOW để kiểm thử.
- Đảm bảo rule Block đặt dưới rule Pass có Schedule.

Lỗi 6: DMZ-WEB không truy cập được
Nguyên nhân:
- Nginx chưa chạy.
- Sai IP DMZ.
- Sai gateway.
- Firewall rule chặn traffic.

Cách khắc phục:
- Kiểm tra nginx bằng systemctl status nginx.
- Kiểm tra các IP 172.16.6.10, .20, .30, .40.
- Kiểm tra gateway 172.16.6.1.
- Kiểm tra Firewall Log và rule LAN/DMZ.


7. KẾT LUẬN

Qua LAB 6, đã thực hiện cấu hình pfSense với các mạng WAN, MGMT, LAN và DMZ; tích hợp Active Directory thông qua LDAP; cấu hình Captive Portal; tạo Alias và Schedule; đồng thời xây dựng các firewall policy kiểm soát truy cập theo địa chỉ IP, port và thời gian.

Kết quả giúp hiểu rõ nguyên lý first-match của firewall rule, cơ chế stateful firewall, vai trò của LDAP trong xác thực người dùng và cách triển khai chính sách phân tách LAN/DMZ trên pfSense.