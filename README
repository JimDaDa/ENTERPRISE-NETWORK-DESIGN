### Báo cáo Cuối kỳ môn Mạng Máy tính Nâng cao
**Đề tài:** THIẾT KẾ HỆ THỐNG MẠNG CÔNG TY DU LỊCH CÓ HAI CHI NHÁNH

#### I. Tổng quan về Mô hình Thiết kế
Hệ thống mạng được thiết kế cho Công ty Du lịch với cấu trúc phân tán gồm hai site chính, đảm bảo tính sẵn sàng cao và khả năng mở rộng:
* **Trụ sở chính (Quận 7):** Quy mô 5 tầng với đầy đủ các phòng ban chức năng.
* **Chi nhánh (Thủ Đức):** Quy mô 2 tầng.
* **Khu vực Server Farm:** Được thiết lập trong vùng riêng biệt (Server Zone), cấu hình IP tĩnh để phục vụ chung cho cả hai chi nhánh.

#### II. Các Khía cạnh Kỹ thuật Chính

#### 1. Hạ tầng Chuyển mạch (Switching) và Phân vùng Mạng
Hệ thống chuyển mạch Layer 2 được thiết kế tối ưu để đảm bảo băng thông và tính ổn định:
* **VLAN (Virtual LAN):** Phân hoạch mạng chi tiết theo phòng ban:
    * *Quận 7 (11 VLAN):* Lễ tân, Hành chính, PGĐ, Giám đốc, Kế toán, Kinh doanh, Nhân sự, Kỹ thuật, Marketing, Du lịch Nội địa/Nước ngoài, Khách hàng (VLAN 200).
    * *Thủ Đức (5 VLAN):* Tiếp tân, Du lịch Nội địa/Nước ngoài, Kỹ thuật, Hành chính.
* **VTP (VLAN Trunking Protocol):** Triển khai với tên miền **`dulichquan7.com`** và cấu hình các chế độ **Server/Transparent** để đồng bộ hóa cơ sở dữ liệu VLAN, giúp quản lý tập trung trên toàn hệ thống.
* **Spanning-tree Protocol (STP):** Kích hoạt cơ chế chống lặp vòng (Loop Prevention) ở Layer 2, đảm bảo sự ổn định của mạng chuyển mạch tránh hiện tượng Broadcast Storm.
* **Port-channel (EtherChannel):** Gộp các cổng vật lý (ví dụ: `G1/0/23-24`, `G1/0/21-22`) thành một liên kết logic nhằm tăng băng thông và cung cấp khả năng dự phòng (Redundancy) cho các đường Uplink giữa lớp Core và Distribution.

#### 2. Định tuyến, Dự phòng Gateway và Cấp phát IP
* **Định tuyến động (Dynamic Routing):**
    * Sử dụng **EIGRP** cho mạng nội bộ IPv4 (được kiểm chứng qua log hệ thống `DUAL-5-NBRCHANGE`), giúp thiết lập lân cận và cập nhật tuyến đường nhanh chóng.
    * Triển khai song song **OSPFv3** để phục vụ định tuyến cho nền tảng IPv6.
* **HSRP (Hot Standby Router Protocol):** Cấu hình trên các Switch Distribution (ví dụ: `DISTRIBUTE1_07` và `DISTRIBUTE2_07`) để tạo Gateway ảo dự phòng. Cơ chế này đảm bảo nếu một Switch bị lỗi, lưu lượng sẽ tự động chuyển sang Switch còn lại mà không làm gián đoạn kết nối của người dùng.
* **IP Planning & DHCP:**
    * Sử dụng kỹ thuật **VLSM** để quy hoạch địa chỉ IPv4/IPv6 tối ưu.
    * **DHCP Relay:** Cấu hình trên các Switch L3 để chuyển tiếp yêu cầu xin IP từ các VLAN đến DHCP Server tập trung.
* **NAT (Network Address Translation):** Cấu hình **PAT (Port Address Translation)** cho phép nhiều thiết bị trong mạng nội bộ (Inside) truy cập Internet thông qua một số lượng nhỏ địa chỉ IP công cộng (Outside).

#### 3. Bảo mật Hệ thống (Security)
* **Xác thực AAA & Radius Server:** Xây dựng hệ thống xác thực tập trung sử dụng Radius Server (IP `10.10.10.7` tại Quận 7 và `10.10.11.6` tại Thủ Đức). Hệ thống này phục vụ xác thực 802.1x cho kết nối **WLAN WPA2 Enterprise** và quản trị thiết bị.
* **DHCP Snooping:** Triển khai tính năng bảo mật Layer 2 này để ngăn chặn các máy chủ DHCP giả mạo (Rogue DHCP), bảo vệ tính toàn vẹn của quá trình cấp phát IP.
* **Port Security:** Áp dụng trên các cổng Access để giới hạn số lượng địa chỉ MAC truy cập, ngăn chặn thiết bị lạ cắm vào mạng.
* **Firewall (ASA) & ACL:**
    * Sử dụng Firewall ASA kết hợp Access Control List (ACL) để kiểm soát luồng dữ liệu.
    * *Chính sách cụ thể:* Cấm Khách hàng truy cập các dịch vụ nội bộ nhạy cảm (FTP/SSH/Mail) và Ping vào VLAN phòng ban; chỉ cho phép truy cập Web. Phân quyền SSH chặt chẽ cho đội ngũ Kỹ thuật theo từng khu vực.
* **VPN (IPsec Tunnel):** Thiết lập đường hầm VPN Site-to-Site mã hóa bằng giao thức **IPsec** để kết nối an toàn giữa Quận 7 và Thủ Đức qua môi trường Internet. 

#### 4. Dịch vụ Quản lý và Chất lượng Mạng (Services & QoS)
* **QoS (Quality of Service):** Được cấu hình trên các Switch Distribution (`DIS1_Q7`, `DIS2_Q7`) để đảm bảo hiệu năng cho các dịch vụ quan trọng:
    * **Class-map:** Phân loại lưu lượng (MAIL, WEB, FTP, REMOTE, ICMP).
    * **Policy-map:** Cấp phát băng thông và đánh dấu gói tin (ví dụ: `set ip dscp af11` cho Mail) để ưu tiên xử lý trước các lưu lượng ít quan trọng hơn.
* **Quản lý và Giám sát:**
    * **NTP (Network Time Protocol):** Đồng bộ thời gian chuẩn cho toàn bộ thiết bị mạng.
    * **Syslog:** Cấu hình gửi log tập trung về Log Server để theo dõi sự kiện và hỗ trợ khắc phục sự cố (Troubleshooting).
* **WLAN (Wireless LAN):** Sử dụng mô hình **Lightweight Access Point** quản lý bởi **WLC (Wireless Lan Controller)**, tách biệt SSID cho Nhân viên (WPA2 Enterprise) và Khách hàng (WPA2 Personal).
* **Voice over IP:** Triển khai dịch vụ thoại (Telephony Service) với dải số mở rộng (ephone-dn) từ 1010 đến 1025.

