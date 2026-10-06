# LAB 5 - THIẾT LẬP MÔ HÌNH TƯỜNG LỬA pfSense

## 1. Thông tin sinh viên
- **Họ và tên:** Triệu Ngọc Hào
- **MSSV:** 1150080050
- **Lớp:** 11_ĐHCNPM1
- **Tên lab:** LAB 5 - Thiết lập mô hình tường lửa pfSense

## 2. Phiên bản / môi trường
- **Máy thật:** Windows 11 64-bit
- **Phần mềm ảo hóa:** VMware Workstation
- **pfSense:** pfSense CE 2.7.2-RELEASE (amd64)
- **Domain Controller:** Windows Server 2019/2022
- **DMZ-Web:** Windows Server 2019/2022 + IIS
- **LAN-Test:** Ubuntu Server 22.04/24.04 LTS minimal

## 3. Mô hình mạng
- **WAN:** Bridged, nhận IP bằng DHCP.
- **LAN:** VMware Host-only VMnet1, mạng `10.0.0.0/8`.
- **DMZ:** VMware LAN Segment `dmz-net`, mạng `172.16.0.0/16`.

### Địa chỉ IP chính
- pfSense LAN: `10.0.0.1/8`
- Domain Controller: `10.0.0.2/8`
  - Gateway: `10.0.0.1`
  - DNS: `10.0.0.2`
- Máy thật VMnet1: `10.0.0.100/8`
  - Gateway: để trống
  - DNS: để trống
- pfSense DMZ: `172.16.0.1/16`
- DMZ-Web: `172.16.0.2/16`
  - Gateway: `172.16.0.1`
- LAN-Test: `10.0.0.3/8`
  - Gateway: `10.0.0.1`

## 4. Cách dựng môi trường
1. Kiểm tra `ipconfig` và `route print` trên máy thật.
2. Trong VMware Virtual Network Editor:
   - VMnet1 = Host-only.
   - Subnet = `10.0.0.0/8`.
   - Tắt DHCP.
3. Đặt VMware Network Adapter VMnet1 trên máy thật:
   - IP = `10.0.0.100/8`.
   - Không đặt Gateway/DNS.
4. Tạo VM pfSense:
   - RAM 2 GB, 2 CPU, disk 16-20 GB.
   - NIC 1 = Bridged (WAN).
   - NIC 2 = VMnet1 (LAN).
   - NIC 3 = LAN Segment `dmz-net` (DMZ).
5. Cài pfSense CE 2.7.2, đặt LAN = `10.0.0.1/8`, không bật DHCP LAN.
6. Truy cập WebGUI tại `https://10.0.0.1`.
7. Tạo Domain Controller trên VMnet1:
   - `10.0.0.2/8`, GW `10.0.0.1`, DNS `10.0.0.2`.
   - Cài AD DS và cấu hình DNS Forwarder.
8. Tạo DMZ trên pfSense: `172.16.0.1/16`.
9. Tạo DMZ-Web trên LAN Segment `dmz-net`:
   - `172.16.0.2/16`, GW `172.16.0.1`.
   - Cài IIS.
10. Kiểm tra Outbound NAT.
11. Disable Default allow LAN to any IPv4/IPv6, giữ Anti-Lockout Rule.
12. Reset States sau mỗi lần thay đổi rule khi kiểm thử.

## 5. Các tình huống đã thực hiện

> README này được chuẩn bị trước khi thực hành. Chỉ đổi trạng thái sang PASS/FAIL sau khi có kiểm thử thực tế.

### Tình huống 1 - Chặn ICMP nhưng vẫn cho phép Web/DNS
- **Trạng thái:** CHƯA THỰC HIỆN
- Kết quả mong đợi:
  - Ping `8.8.8.8`: FAIL
  - DNS: PASS
  - HTTPS: PASS

### Tình huống 2 - Chỉ cho một host cụ thể ra Internet
- **Trạng thái:** CHƯA THỰC HIỆN
- Kết quả mong đợi:
  - DC `10.0.0.2` ra Internet: PASS
  - LAN-Test `10.0.0.3` ra Internet: FAIL

### Tình huống 3 - Cô lập DMZ khỏi LAN
- **Trạng thái:** CHƯA THỰC HIỆN
- Kết quả mong đợi:
  - Baseline DMZ -> DC trước Block: PASS
  - Sau Block DMZ -> LAN: FAIL
  - DMZ -> Internet: PASS

### Tình huống 4 - Port Forward WAN -> DMZ
- **Trạng thái:** CHƯA THỰC HIỆN
- Kết quả mong đợi:
  - Truy cập `http://<WAN-pfSense>:8080` thấy trang IIS: PASS

### Tình huống 5 - Bật logging và đọc Firewall Log
- **Trạng thái:** CHƯA THỰC HIỆN
- Kết quả mong đợi:
  - Traffic bị Block xuất hiện trong Firewall Log: PASS

## 6. Tổng kết PASS/FAIL
- **Số tình huống PASS:** Chưa xác định
- **Số tình huống FAIL:** Chưa xác định
- **Trạng thái tổng thể:** CHƯA THỰC HIỆN

## 7. Lỗi gặp phải và cách khắc phục
Hiện chưa thực hành nên chưa có lỗi thực tế. Sau khi làm lab, ghi theo mẫu:

- **Lỗi:**  
- **Nguyên nhân:**  
- **Cách khắc phục:**  
- **Kết quả sau khắc phục:**  

Các lỗi thường cần kiểm tra:
- WAN/LAN/DMZ bị overlap subnet.
- VMnet1 chưa tắt DHCP.
- Quên Reset States sau khi đổi firewall rule.
- Rule Block nằm dưới rule Pass tổng quát.
- Windows Firewall chặn ICMP/TCP.
- IIS chưa chạy trước khi test Port Forward.

## 8. Tài liệu
- `Lab5-pfSense_SAMPLE_STYLE_SPLIT_FIGURES_v6_FINAL_TEXT_ONLY.docx`
- README gốc đi kèm gói lab
