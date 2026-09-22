# LAB3 – Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin

## 1. Thông tin sinh viên

* **Họ và tên:** Triệu Ngọc Hào
* **MSSV:** 1150080050
* **Lớp:** 11_ĐHCNPM1
* **Tên bài thực hành:** LAB3 – Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin


---

## 2. Môi trường thực hiện

* **Máy ảo:** Windows 10 Pro 64-bit
* **Phần mềm ảo hóa:** VMware Workstation
* **PowerShell:** 5.1 (Run as Administrator)
* **Microsoft Defender:** Windows Defender Antivirus (Real-time protection + Tamper Protection bật)
* **Sysmon:** 15.22
* **Autoruns:** 14.3
* **Process Explorer:** 17.14
* **Wireshark:** 4.6.8 (kèm Npcap – hỗ trợ Loopback Capture)
* **Python:** 3.x (dùng chạy HTTP server cục bộ và script load test)

---

## 3. Thiết lập môi trường

Thư mục làm việc của LAB3 trên máy ảo:

```text
C:\LAB3\
├── Evidence\
├── Tools\
├── Downloads\
└── Assets\
```

Các bước thiết lập chính (theo Mục A.5 của đề bài):

1. Tạo máy ảo, cấu hình Network Adapter = Host-only trong VMware Workstation.
2. Tạo cấu trúc thư mục `C:\LAB3` (Evidence, Tools, Downloads, Assets) và ghi mốc thời gian bắt đầu (`start_time.txt`).
3. Giải nén gói `LAB3_Threats_Assets.zip` sau khi kiểm tra SHA-256 khớp manifest.
4. Cài đặt Python và Wireshark (giữ Npcap khi cài Wireshark).
5. Tải và giải nén bộ Sysinternals (Sysmon, Autoruns, Process Explorer) **chỉ từ** `download.sysinternals.com`.
6. Kiểm tra version từng công cụ bằng PowerShell (ảnh H2).
7. Chạy baseline hệ thống (OS, Defender, Firewall, network, process) trước khi tạo bất kỳ tình huống nào (ảnh H3).

---

## 4. Nội dung thực hiện

### TH1 – Nhận diện tài sản, lỗ hổng, mối đe dọa và rủi ro
* Xây dựng Risk Register cho 5 tài sản: thông tin xác thực tài khoản, dữ liệu/tệp bằng chứng, dịch vụ mạng giả lập (HTTP 8080), hộp thư/kênh giao tiếp, nhật ký sự kiện hệ thống.
* Mỗi hàng gắn đúng chuỗi Asset → Vulnerability → Threat → Risk → Control.
* Phân loại 5 tình huống theo 5 nhóm nguồn đe dọa: hành động vô ý, hành động cố ý, thảm họa tự nhiên, lỗi kỹ thuật, lỗi quản lý — kèm giải thích căn cứ theo định nghĩa bài học.
* Ảnh minh chứng: **H1** (cấu trúc thư mục), **H3** (Defender/Firewall baseline).

### TH2 – Malware và Endpoint Protection
* Kiểm tra `Get-MpComputerStatus`: RealTimeProtectionEnabled = True.
* Tạo tệp EICAR test string trong `C:\LAB3\Evidence\eicar.com.txt`.
* Xác nhận Defender phát hiện/cách ly qua `Get-MpThreatDetection` và Windows Security > Protection History.
* Không tắt Defender, không tạo exclusion, không phục hồi tệp bị quarantine.
* Ảnh minh chứng: **H4** (Protection History – EICAR).

### TH3 – Tấn công mật khẩu và nguy cơ Keylogging
* Bật audit Logon success/failure bằng `auditpol` (GUID subcategory Logon).
* Tạo tài khoản thử nghiệm cục bộ `lab3user`.
* Sinh 1 lần đăng nhập thành công (`runas` đúng mật khẩu) và 2 lần đăng nhập thất bại có kiểm soát.
* Thu thập Event ID **4624** (thành công), **4625** (thất bại), **4648** (logon với credential khác) trong Security log.
* Đổi mật khẩu (`Set-LocalUser`), kiểm chứng: mật khẩu cũ không còn dùng được, mật khẩu mới hoạt động.
* Ảnh minh chứng: **H5** (Event Viewer – Event ID 4625 của lab3user).

### TH4 – Backdoor: Persistence và dịch vụ lắng nghe không phê duyệt
* Cài Sysmon 15.22 với cấu hình `sysmon-lab.xml`, xác nhận Event ID 1 (Process Create) khi mở Notepad.
* Tạo hai artefact lành tính: Registry Run key `LAB3_Run_Demo` và Scheduled Task `LAB3_Persistence_Demo`.
* Xác minh persistence còn tồn tại sau logon; kiểm tra bằng Autoruns (tab Logon).
* Chạy HTTP server chỉ bind `127.0.0.1:8080`; dùng `Get-NetTCPConnection` lấy PID, đối chiếu bằng Process Explorer (Image, Command line, Verified Signer).
* Ảnh minh chứng: **H6** (Sysmon Event ID 1), **H7** (Autoruns – LAB3_Run_Demo), **H8** (Process Explorer – python.exe theo đúng PID).

### TH5 – Sniffing, MITM, Spoofing: HTTP so với HTTPS
* Capture trên interface loopback bằng Wireshark, chạy `curl.exe` tới `http://127.0.0.1:8080/?lab_user=...&lab_code=TRAINING_ONLY` → đọc được Request URI dạng plaintext.
* So sánh với traffic HTTPS/TLS (`curl.exe -I https://example.com/...`) → chỉ thấy metadata (IP, cổng, kích thước, một phần thông tin handshake), không đọc được nội dung.
* Không thực hiện ARP poisoning, DNS spoofing, Wi-Fi giả mạo, session hijacking hoặc chèn chứng chỉ.
* Ảnh minh chứng: **H9** (HTTP plaintext), **H10** (TLS/443).

### TH6 – DoS, DDoS và Mail Bombing
* Chạy `local_load_test.py` — script hard-code **127.0.0.1:8080, 50 request / 5 worker**, không sửa để trỏ mục tiêu khác.
* Ghi lại: số request, số worker, số ok/failures, elapsed_s, avg_latency_s.
* Phân tích `ddos_sample.csv` (dataset TEST-NET, nhiều SourceIP phân tán) và `mailbomb_sample.csv` (thống kê sender/volume bất thường) — hoàn toàn offline, không gửi email hay tạo DDoS thật.
* Ảnh minh chứng: **H11** (kết quả local load test + phân tích log DDoS/Mail).

### TH7 – Social Engineering, Phishing, Spear Phishing
* Phân tích `phishing_email.txt` offline, xác định các chỉ dấu: uy tín giả (display name giả IT Support), khẩn cấp (thời hạn 15 phút), domain chưa xác thực (`.example`), Reply-To khác From, yêu cầu credential qua link.
* Phân loại các case trong `social_engineering_cases.csv`.


---

## 5. Quy trình xử lý

```text
Baseline → Observe → Detect → Contain → Recover → Verify
```

Toàn bộ bằng chứng lưu tại: `C:\LAB3\Evidence\`

---

## 6. Kết quả thực hiện

| Nội dung | Kết quả |
|---|---|
| TH1 – Threat & Risk | ✅ Hoàn thành – Risk Register 5 tài sản + phân loại 5 nguồn đe dọa |
| TH2 – EICAR & Defender | ✅ Hoàn thành – Defender phát hiện/cách ly EICAR (H4) |
| TH3 – Password Attack & Keylogging | ✅ Hoàn thành – Event 4624/4625 ghi nhận, credential rotation kiểm chứng (H5) |
| TH4 – Persistence & Backdoor | ✅ Hoàn thành – Sysmon Event 1, Autoruns, Process Explorer khớp PID (H6–H8) |
| TH5 – Sniffing & Wireshark | ✅ Hoàn thành – HTTP plaintext và TLS/443 (H9–H10) |
| TH6 – DoS/DDoS & Mail Bombing | ✅ Hoàn thành – local_load_test 50 request/5 worker, phân tích dataset (H11) |
| TH7 – Phishing & Social Engineering | ⚠️ Cần bổ sung – mới có 4/6 case, sai nhãn phân loại so với đề bài |
| Thu thập Evidence | ✅ 11 ảnh (H1–H11) + log text |
| SHA-256 Evidence | ✅ `evidence_sha256.csv` đã tạo cho toàn bộ file Evidence |
| Recovery & Verification | ✅ Đã gỡ Run key, Scheduled Task, dừng HTTP server, Defender vẫn bật sau cleanup |

---

## 7. Lỗi gặp phải và cách khắc phục

---

## 8. Evidence

Lưu tại `C:\LAB3\Evidence\`, bao gồm:
* Log text: `baseline_os.txt`, `baseline_defender.txt`, `baseline_firewall.txt`, `baseline_network.txt`, `baseline_processes.txt`, `defender_eicar.txt`, `auth_events_before_rotation.txt`, `sysmon_persistence.txt`, `local_load_test.txt`, `ddos_sources.txt`, `mail_sender_counts.txt`, `mail_volume.txt`, `autoruns_before.csv`, `autoruns_after.csv`, `autoruns_diff.txt`.
* File toàn vẹn: `evidence_sha256.csv` (SHA-256 của toàn bộ Evidence).

Tất cả log/ảnh phải khớp timestamp thật của quá trình thực hành, không dùng ảnh của người khác, không chứa mật khẩu/token/dữ liệu cá nhân thật.

---

## 9. Cấu trúc thư mục LAB3 trên Repository

```text
LAB_AT_BMHTTT/
└── LAB3/
    ├── README.md
    ├── 11_ĐHCNPM1-LAB3_1150080050-TrieuNgocHao.docx   
    ├── Evidence/
    │   ├── H1_VM_WindowsVersion.png
    │   ├── H2_ToolVersions.png
    │   ├── H3_Baseline_Defender_Firewall.png
    │   ├── H4_ProtectionHistory_EICAR.png
    │   ├── H5_Event4625.png
    │   ├── H6_Sysmon_Event1.png
    │   ├── H7_Autoruns_LAB3_Run_Demo.png
    │   ├── H8_ProcessExplorer_Python.png
    │   ├── H9_HTTP_Plaintext.png
    │   ├── H10_TLS_443.png
    │   ├── H11_Load_and_Log_Analysis.png
    │   ├── *.txt / *.csv (log đã làm sạch)
    │   └── evidence_sha256.csv
    
```