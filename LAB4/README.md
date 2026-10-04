# LAB4 – Khảo sát và đánh giá bề mặt mạng bằng Nmap

## 1. Thông tin sinh viên

Họ và tên: Triệu Ngọc Hào
MSSV: 1150080050
Lớp: 11_ĐHCNPM1
Tên lab: LAB4 – Khảo sát và đánh giá bề mặt mạng bằng Nmap


## 2. Phiên bản môi trường

Máy thật: Windows 10/11 64-bit
Phần mềm ảo hóa: VMware Workstation
Mạng thực hành: VMware Host-only – VMnet1
Subnet: 192.168.255.0/24
Máy quét: Kali Linux
Máy mục tiêu: Metasploitable 2
Nmap: 7.7.9 

Địa chỉ IP thực tế:

Kali Linux: 192.168.255.129
Metasploitable 2: 192.168.255.128


## 3. Cách dựng môi trường

1. Cài VMware Workstation trên máy Windows.
2. Tạo máy ảo Kali Linux từ file ISO.
3. Import Metasploitable 2 vào VMware.
4. Cấu hình cả Kali Linux và Metasploitable 2 sử dụng mạng VMnet1 – Host-only.
5. Bật DHCP cho VMnet1.
6. Kiểm tra IP Kali bằng lệnh:

ip -br addr

7. Kiểm tra IP Metasploitable 2 bằng lệnh:

ifconfig

8. Kiểm tra kết nối từ Kali tới Metasploitable bằng lệnh:

ping -c 4 192.168.255.128

9. Kiểm tra phiên bản Nmap:

nmap --version

10. Chỉ thực hiện scan trong mạng Host-only của LAB4.


## 4. Các tình huống đã thực hiện

### TH1 – Xác định IP và kiểm tra kết nối

- Xác định IP Kali bằng ip -br addr.
- Xác định IP Metasploitable bằng ifconfig.
- Ping từ Kali tới Metasploitable.
- Xác nhận hai máy cùng subnet Host-only.

Kết quả: PASS


### TH2 – Host Discovery

Lệnh:

sudo nmap -sn 192.168.255.0/24

Nội dung thực hiện:

- Phát hiện các host đang hoạt động.
- Ghi nhận IP.
- Ghi MAC/Vendor nếu có.
- Xác định Kali, Metasploitable và VMware host adapter.

Kết quả: PASS


### TH3 – TCP Connect Scan

Lệnh:

nmap -sT 192.168.255.128

Nội dung thực hiện:

- Xác định các cổng TCP.
- Quan sát trạng thái open, closed, filtered.
- Ghi nhận dịch vụ trên các port mở.

Kết quả: PASS


### TH4 – SYN Scan

Lệnh:

sudo nmap -sS 192.168.255.128

Nội dung thực hiện:

- So sánh với TCP Connect Scan.
- Quan sát sự khác nhau về cơ chế và quyền thực thi.

Kết quả: PASS


### TH5 – FIN / Xmas / NULL Scan

Lệnh:

sudo nmap -sF 192.168.255.128
sudo nmap -sX 192.168.255.128
sudo nmap -sN 192.168.255.128

Nội dung thực hiện:

- Quan sát phản ứng TCP của máy mục tiêu.
- Phân tích trạng thái open|filtered.

Kết quả: PASS


### TH6 – ACK Scan

Lệnh:

sudo nmap -sA 192.168.255.128

Nội dung thực hiện:

- Quan sát trạng thái filtered và unfiltered.
- So sánh với SYN scan.

Kết quả: PASS


### TH7 – UDP Scan

Lệnh:

sudo nmap -sU --top-ports 20 192.168.255.128

Nội dung thực hiện:

- Quét 20 cổng UDP phổ biến.
- Quan sát trạng thái open, closed, open|filtered.

Kết quả: PASS


### TH8 – Service Version Detection

Lệnh:

sudo nmap -sV 192.168.255.128

Nội dung thực hiện:

- Xác định dịch vụ đang chạy.
- Xác định phiên bản phần mềm.
- Ghi nhận các dịch vụ như FTP, SSH, HTTP, SMB, MySQL nếu xuất hiện.

Kết quả: PASS


### TH9 – OS Detection

Lệnh:

sudo nmap -O 192.168.255.128

Nội dung thực hiện:

- Fingerprinting hệ điều hành.
- Ghi nhận OS family, kernel hoặc thông tin Nmap suy đoán.

Kết quả: PASS


### TH10 – Aggressive Scan

Lệnh:

sudo nmap -A 192.168.255.128

Nội dung thực hiện:

- Version detection.
- OS detection.
- Default NSE scripts.
- Traceroute.

Kết quả: PASS


### TH11 – SMB Information

Lệnh:

sudo nmap -p 445 --script smb-os-discovery 192.168.255.128

Nội dung thực hiện:

- Thu thập thông tin hệ điều hành.
- Computer name.
- Domain/Workgroup nếu script trả về.

Kết quả: PASS


### TH12 – Kiểm tra MS17-010

Lệnh:

sudo nmap -p 445 --script smb-vuln-ms17-010 192.168.255.128

Nội dung thực hiện:

- Kiểm tra dấu hiệu ảnh hưởng bởi MS17-010.
- Không thực hiện khai thác lỗ hổng.

Nếu script trả về VULNERABLE thì ghi nhận có dấu hiệu dễ bị ảnh hưởng.
Nếu script timeout hoặc không kết nối được thì không kết luận máy đã vá.

Kết quả: [PASS/FAIL theo output thực tế]


### TH13 – Xuất kết quả Nmap

Tạo thư mục:

mkdir -p ~/LAB4
cd ~/LAB4

Xuất normal text:

sudo nmap -sV -O 192.168.255.128 -oN ket_qua.txt

Xuất XML:

sudo nmap -sV -O 192.168.255.128 -oX ket_qua.xml

Xuất grepable:

sudo nmap -p 445 192.168.255.0/24 -oG smb.txt

Kiểm tra:

ls -l

Kết quả: PASS


## 5. Tổng hợp kết quả PASS/FAIL

Thiết lập VMware Host-only: PASS
Kali Linux hoạt động: PASS
Metasploitable 2 hoạt động: PASS
Kali ↔ Metasploitable kết nối được: PASS
Host Discovery: PASS
TCP Connect Scan: PASS
SYN Scan: PASS
FIN / Xmas / NULL Scan: PASS
ACK Scan: PASS
UDP Scan: PASS
Service Detection: PASS
OS Detection: PASS
Aggressive Scan: PASS
SMB Discovery: PASS
MS17-010 NSE: [PASS/FAIL]
Xuất file kết quả: PASS


## 6. Lỗi gặp phải và cách khắc phục

### Lỗi 1 – Metasploitable không nhận địa chỉ IP

Hiện tượng:

Lệnh ifconfig không hiển thị IPv4 trên eth0.

Khi yêu cầu DHCP xuất hiện nhiều dòng:

DHCPDISCOVER

nhưng không nhận được IP.

Nguyên nhân:

VMnet1 chưa bật DHCP hoặc card mạng của VM chưa được nối đúng vào Host-only.

Cách khắc phục:

1. Mở VMware → Edit → Virtual Network Editor.
2. Chọn VMnet1.
3. Cấu hình Host-only.
4. Bật:

Use local DHCP service to distribute IP addresses to VMs

5. Đặt Network Adapter của Kali và Metasploitable về VMnet1.
6. Khởi động lại VM.
7. Kiểm tra lại bằng ifconfig.

Kết quả sau khắc phục: PASS


### Lỗi 2 – Aggressive Scan chạy lâu

Hiện tượng:

Lệnh:

sudo nmap -A 192.168.255.128

dừng lâu ở:

Script Scan
NSE Timing

Nguyên nhân:

-A thực hiện đồng thời version detection, OS detection, traceroute và các NSE script mặc định.

Cách khắc phục:

Chờ quá trình scan hoàn tất và kiểm tra khi Nmap trả lại terminal prompt.

Kết quả sau khắc phục: PASS


## 7. Evidence

Các ảnh minh chứng chính:

H1_Kali_IP.png
H2_Metasploitable_IP.png
H3_Host_Discovery.png
H4_SYN_Scan.png
H5_Service_Version.png
H6_Aggressive_Scan.png
H7_NSE_MS17_010.png
H8_Output_Files.png

File kết quả:

ket_qua.txt
ket_qua.xml
smb.txt


## 8. Cấu trúc repository

LAB_AT_BMHTTT/
└── LAB4/
    ├── README.md
    ├── 11_ĐHCNPM1-LAB4_1150080050-TrieuNgocHao.docx
        ├── Images/
        │   ├── H1_Kali_IP.png
        │   ├── H2_Metasploitable_IP.png
        │   ├── H3_Host_Discovery.png
        │   ├── H4_SYN_Scan.png
        │   ├── H5_Service_Version.png
        │   ├── H6_Aggressive_Scan.png
        │   ├── H7_NSE_MS17_010.png
        │   └── H8_Output_Files.png
        └── Nmap/
            ├── ket_qua.txt
            ├── ket_qua.xml
            └── smb.txt
