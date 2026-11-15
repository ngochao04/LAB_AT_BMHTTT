https://youtu.be/mIr7ztLoNA8

# LAB 8 – LOCAL DNS ATTACK: DNS SPOOFING VÀ CACHE POISONING

## 1. THÔNG TIN SINH VIÊN

Họ và tên: Triệu Ngọc Hào  
MSSV: 1150080050  
Lớp: 11DHCNPM1  
Tên bài thực hành: LAB 8 – Local DNS Attack

## 2. PHIÊN BẢN VÀ MÔI TRƯỜNG THỰC HIỆN

Phần mềm ảo hóa:
- Oracle VirtualBox

Firewall/Gateway:
- pfSense CE 2.7.2
- WAN: 10.0.2.15/24
- LAN: 10.6.0.1/24
- DMZ: 172.16.6.1/24
- DNSLAB: 10.9.0.1/24

Máy DNS-SERVER:
- Ubuntu Server 24.04 LTS
- Máy ảo: WEB_PROXY (tận dụng từ LAB 7)
- IP mục tiêu: 10.9.0.10/24
- Gateway: 10.9.0.1
- Dịch vụ DNS: BIND9

Máy VICTIM:
- Windows 10
- Máy ảo: ClinetA
- IP mục tiêu: 10.9.0.100/24
- DNS: 10.9.0.10

Máy ATTACKER:
- Kali Linux
- IP mục tiêu: 10.9.0.200/24
- Công cụ: Scapy, Wireshark, tcpdump

Máy chủ DMZ:
- Ubuntu Server
- DMZ-WEB: 172.16.6.10
- Gateway: 172.16.6.1

AUTH-DNS:
- IP theo tài liệu: 172.16.6.20
- Chức năng: DNS authoritative mô phỏng

Mô hình mạng:
- WAN: NAT
- LAN-DOMAIN: 10.6.0.0/24
- DMZ: 172.16.6.0/24
- DNSLAB: 10.9.0.0/24

Domain thử nghiệm:
- lab9.test
- www.lab9.test
- external.lab9.test
- www.external.lab9.test

## 3. CÁCH DỰNG MÔI TRƯỜNG

- Sử dụng Oracle VirtualBox để quản lý máy ảo.
- Tận dụng pfSense đã cấu hình trong các LAB trước.
- Thêm Adapter 4, kết nối Internal Network với tên dns-lab.
- Gán interface le3 cho DNSLAB trong pfSense.
- Cấu hình địa chỉ 10.9.0.1/24 cho DNSLAB.
- Không cấu hình Upstream Gateway trên interface nội bộ.
- Tạo firewall rules để cô lập DNSLAB với LAN-DOMAIN và các mạng không được phép truy cập.
- Tận dụng WEB_PROXY Ubuntu Server 24.04 để triển khai DNS-SERVER.
- Chuẩn bị cấu hình Netplan để chuyển WEB_PROXY sang mạng DNSLAB.
- Sử dụng ClinetA làm máy VICTIM.
- Sử dụng Kali Linux làm máy ATTACKER.
- Sử dụng DMZ-WEB làm website hợp lệ trong môi trường thử nghiệm.
- Mô hình sử dụng mạng ảo nội bộ, không thực hiện tấn công trên Internet hoặc mạng công cộng.

Firewall rules đã cấu hình:

1. PASS: 10.9.0.10 -> 172.16.6.20, TCP/UDP 53.
2. PASS: 10.9.0.0/24 -> 172.16.6.10, TCP 80.
3. BLOCK: 10.9.0.0/24 -> 10.6.0.0/24, Any.
4. BLOCK: 10.9.0.0/24 -> 192.168.56.0/24, Any.
5. BLOCK: 10.9.0.0/24 -> Any, Any.

Các rule được sắp xếp theo thứ tự cho phép lưu lượng hợp lệ trước, sau đó chặn những lưu lượng không được phép.

## 4. CÁC TÌNH HUỐNG THỰC HÀNH

### Tình huống 1: Cấu hình DNSLAB trên pfSense

- Tạo Internal Network dns-lab trong VirtualBox.
- Gán card le3 cho interface DNSLAB.
- Cấu hình địa chỉ IP 10.9.0.1/24.
- Tạo 5 firewall rules.
- Apply Changes để áp dụng cấu hình.
- Kiểm tra danh sách firewall rules trong WebGUI.

### Tình huống 2: Chuẩn bị DNS-SERVER

- Sử dụng Ubuntu Server 24.04 từ máy WEB_PROXY.
- Kiểm tra interface bằng ip -br addr.
- Kiểm tra gateway bằng ip route.
- Xác định file cấu hình Netplan.
- Chỉnh sửa cấu hình mạng cho DNSLAB.
- Kiểm tra cú pháp YAML và quyền truy cập file.

### Tình huống 3: Baseline DNS

Mục tiêu:
- DNS-SERVER phân giải tên miền thử nghiệm.
- Truy vấn www.external.lab9.test trả về 172.16.6.10.
- VICTIM truy cập website hợp lệ trên DMZ-WEB.
- Kiểm tra gói DNS bằng Wireshark.

### Tình huống 4: HOSTS File Redirection

Mục tiêu:
- Thay đổi ánh xạ tên miền trong file hosts của VICTIM.
- Truy cập tên miền thử nghiệm và quan sát kết quả.
- So sánh kết quả phân giải qua hệ điều hành với truy vấn DNS trực tiếp.
- Khôi phục file hosts sau thử nghiệm.

### Tình huống 5: Direct DNS Spoofing

Mục tiêu:
- Quan sát truy vấn DNS trong mạng cô lập.
- Phân tích Transaction ID, UDP source port và query name.
- Đối chiếu phản hồi DNS hợp lệ và phản hồi giả.
- Xác định điều kiện để VICTIM chấp nhận phản hồi.

### Tình huống 6: DNS Cache Poisoning

Mục tiêu:
- Phân tích hoạt động của caching resolver.
- Quan sát sự khác biệt giữa phản hồi gửi trực tiếp đến client và phản hồi gửi đến resolver.
- Kiểm tra nội dung DNS cache và TTL.
- Phân tích khả năng ảnh hưởng đến nhiều client dùng chung resolver.

### Tình huống 7: Khôi phục và phòng thủ

Mục tiêu:
- Khôi phục cấu hình DNS hợp lệ.
- Xóa dữ liệu không mong muốn khỏi DNS cache.
- Đưa Promiscuous Mode về Deny.
- Kiểm tra DNSSEC, DNS COOKIE và cơ chế phân đoạn mạng.
- Xác nhận các dịch vụ hoạt động bình thường.

## 5. KẾT QUẢ PASS/FAIL

Kịch bản 1 – Cấu hình pfSense:

- Tạo interface DNSLAB: PASS.
- Gán interface le3: PASS.
- Đặt địa chỉ 10.9.0.1/24: PASS.
- Tạo 5 firewall rules: PASS.
- Apply Changes: PASS.
- Kết quả: PASS.

Kịch bản 2 – Chuẩn bị DNS-SERVER:

- Đăng nhập Ubuntu Server: PASS.
- Xác định card enp0s3: PASS.
- Kiểm tra Netplan: PASS.
- Chuyển IP sang 10.9.0.10: Chưa xác nhận.
- Kết quả: Chưa hoàn tất.

Kịch bản 3 – Baseline DNS:
- Chưa có kết quả kiểm thử.

Kịch bản 4 – HOSTS File Redirection:
- Chưa có kết quả kiểm thử.

Kịch bản 5 – Direct DNS Spoofing:
- Chưa có kết quả kiểm thử.

Kịch bản 6 – DNS Cache Poisoning:
- Chưa có kết quả kiểm thử.

Kịch bản 7 – Khôi phục và Hardening:
- Chưa có kết quả kiểm thử.

## 6. LỖI GẶP PHẢI VÀ CÁCH KHẮC PHỤC

### Lỗi 1: Không truy cập được pfSense WebGUI từ máy thật

Nguyên nhân:
- Các Adapter LAN và DMZ sử dụng Internal Network.
- Máy thật không kết nối trực tiếp vào các mạng này.

Cách khắc phục:
- Khởi động máy ClinetA trong mạng lab6-lan.
- Truy cập pfSense tại https://10.6.0.1.
- Đăng nhập WebGUI và tiếp tục cấu hình.

### Lỗi 2: Không tìm thấy Adapter 5 trong VirtualBox

Nguyên nhân:
- Giao diện Settings chỉ hiển thị Adapter 1 đến Adapter 4.

Cách khắc phục:
- Kiểm tra Adapter 4 chưa sử dụng.
- Tận dụng Adapter 4 làm dns-lab.
- Kiểm tra pfSense nhận card mới là le3.

### Lỗi 3: Không rõ cách gán interface DNSLAB

Nguyên nhân:
- Console Assign Interfaces có thể yêu cầu gán lại WAN/LAN.
- Gán sai interface có nguy cơ ảnh hưởng cấu hình cũ.

Cách khắc phục:
- Không gán lại các interface đang hoạt động.
- Sử dụng WebGUI.
- Vào Interfaces > Assignments.
- Thêm le3 thành DNSLAB.

### Lỗi 4: Netplan báo Invalid YAML

Nguyên nhân:
- Sai thụt lề trong file YAML.
- Dòng routes hoặc via không đúng cấu trúc.
- Có thể nhập nhầm tên file cloud-init thành clound-init.

Cách khắc phục:
- Kiểm tra danh sách file trong /etc/netplan/.
- Kiểm tra lại nội dung file YAML.
- Sửa thụt lề bằng khoảng trắng.
- Chạy sudo netplan generate để kiểm tra cú pháp.

### Lỗi 5: Netplan cảnh báo quyền truy cập file

Nguyên nhân:
- File cấu hình có quyền truy cập quá rộng.

Cách khắc phục:
- Sử dụng chmod 600 để giới hạn quyền.
- Kiểm tra lại file cấu hình.
- Chạy netplan generate trước khi áp dụng.

## 7. LOG KIỂM TRA

Các công cụ và log sử dụng:

- pfSense: Firewall > Rules > DNSLAB.
- pfSense: Status > System Logs > Firewall.
- Ubuntu: ip -br addr.
- Ubuntu: ip route.
- Ubuntu: ls /etc/netplan/.
- Ubuntu: sudo netplan generate.
- BIND9: journalctl -u bind9.
- DNS: dig và nslookup.
- Wireshark: lọc gói dns.
- tcpdump: quan sát lưu lượng UDP/TCP port 53.

Minh chứng đã có:
- Cấu hình Adapter dns-lab.
- Gán le3 thành DNSLAB.
- Địa chỉ DNSLAB 10.9.0.1/24.
- Bảng 5 firewall rules đã Apply Changes.
- Thông tin mạng WEB_PROXY.
- Thông báo lỗi Netplan trong quá trình cấu hình.

## 8. KẾT LUẬN

LAB 8 tập trung vào mô hình Local DNS Attack, bao gồm HOSTS File Redirection, Direct DNS Spoofing và DNS Cache Poisoning trong mạng ảo cô lập.

Trong quá trình thực hành, đã xây dựng được mạng DNSLAB trên pfSense, cấu hình interface riêng 10.9.0.1/24 và thiết lập các firewall rules kiểm soát truy cập giữa DNSLAB, LAN-DOMAIN và DMZ.

Mô hình tận dụng các máy ảo từ những bài thực hành trước để giảm tài nguyên và thời gian cài đặt.

Phần cấu hình DNS-SERVER đã được chuẩn bị nhưng còn cần hoàn tất Netplan, BIND9 và các tình huống kiểm thử DNS.

Qua phần đã thực hiện, hiểu rõ hơn về cấu hình mạng Internal Network, phân đoạn mạng bằng firewall, quản lý interface trên pfSense và những vấn đề thường gặp khi cấu hình Netplan trên Ubuntu Server.
