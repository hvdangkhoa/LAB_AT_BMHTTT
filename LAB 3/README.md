# BÁO CÁO THỰC HÀNH

**Môn học:** An toàn Hệ thống Thông tin  
**LAB 3**  
**Họ tên sinh viên:** Huỳnh Vũ Đăng Khoa_1150070020  
**Lớp:** 11TMĐT  
Link video : https://youtu.be/x9STm18RUmw
---

## 1. Mục tiêu thực hành
- Làm quen với công cụ quét mạng Nmap.
- Kiểm tra kết nối mạng giữa các máy ảo trong mô hình Host-Only.
- Phát hiện các host đang hoạt động trong mạng.
- Khảo sát các cổng TCP/UDP, nhận diện dịch vụ và hệ điều hành.
- Quan sát kết quả từ các script NSE để tìm kiếm thông tin.
- Biết cách xuất và lưu kết quả quét làm minh chứng.

## 2. Môi trường thực hành
- **Máy quét:** Kali Linux (IP: 192.168.253.129)
- **Máy đích:** Metasploitable 2 (IP: 192.168.253.134)
- **Kết nối:** Mạng nội bộ (Host-Only)

## 3. Nội dung thực hành

### 3.1. Kiểm tra địa chỉ IP máy quét (Kali Linux)
- **Mục đích:** Xác định IP của máy Kali để đảm bảo đang nằm đúng mạng thực hành.
- **Lệnh thực hiện:** `ip -br addr`
- **Nhận xét:** Máy Kali Linux đã nhận địa chỉ IP `192.168.253.129` với interface `eth0` ở trạng thái UP.

### 3.2. Kiểm tra địa chỉ IP máy đích (Metasploitable 2)
- **Mục đích:** Xác định IP của máy đích để làm mục tiêu quét.
- **Lệnh thực hiện:** `ifconfig`
- **Nhận xét:** Máy Metasploitable 2 đang có địa chỉ IP là `192.168.253.134`, cùng lớp mạng với máy Kali.

### 3.3. Kiểm tra kết nối trước khi quét
- **Mục đích:** Đảm bảo máy quét có thể giao tiếp với máy đích qua mạng.
- **Lệnh thực hiện:** `ping -c 4 192.168.253.134`
- **Nhận xét:** Lệnh ping thành công (0% packet loss), chứng tỏ kết nối mạng giữa hai máy ảo hoạt động tốt.

### 3.4. Kiểm tra phiên bản Nmap
- **Mục đích:** Đảm bảo công cụ Nmap đã được cài đặt và sẵn sàng sử dụng.
- **Lệnh thực hiện:** `nmap --version`
- **Nhận xét:** Máy đang cài đặt Nmap phiên bản 7.99.

### 3.5. Phát hiện host đang hoạt động (Host Discovery)
- **Mục đích:** Dùng Nmap để kiểm tra xem mục tiêu có đang hoạt động (up) hay không.
- **Lệnh thực hiện:** `nmap -sn 192.168.253.134`
- **Nhận xét:** Kết quả cho thấy "Host is up", đồng thời phát hiện được MAC Address của VMWare.

### 3.6. Khảo sát cổng bằng kỹ thuật SYN Scan
- **Mục đích:** Quét các cổng TCP trên máy đích với tốc độ nhanh và ít để lại dấu vết hoàn chỉnh.
- **Lệnh thực hiện:** `sudo nmap -sS 192.168.253.134`
- **Nhận xét:** Nmap đã liệt kê rất nhiều cổng đang ở trạng thái open như 21 (ftp), 22 (ssh), 23 (telnet), 80 (http), 445 (microsoft-ds), v.v. Tổng cộng có 977 cổng closed.

### 3.7. Nhận diện phiên bản dịch vụ
- **Mục đích:** Xác định chính xác phiên bản của các dịch vụ đang chạy trên các cổng mở.
- **Lệnh thực hiện:** `nmap -sV 192.168.253.134`
- **Nhận xét:** Nmap đã phát hiện chi tiết phiên bản dịch vụ, ví dụ cổng 21 chạy vsftpd 2.3.4, cổng 22 chạy OpenSSH 4.7p1, cổng 80 chạy Apache httpd 2.2.8.

### 3.8. Nhận diện hệ điều hành
- **Mục đích:** Thu thập thông tin để dự đoán hệ điều hành của máy đích.
- **Lệnh thực hiện:** `sudo nmap -O 192.168.253.134`
- **Nhận xét:** Nmap dự đoán máy đích đang chạy Linux kernel 2.6.X, cụ thể là phiên bản 2.6.9 - 2.6.33.

### 3.9. Quét tổng hợp (Aggressive Scan)
- **Mục đích:** Thực hiện nhận diện HĐH, dịch vụ, chạy script mặc định và traceroute trong một lệnh.
- **Lệnh thực hiện:** `sudo nmap -A 192.168.253.134`
- **Nhận xét:** Lệnh `-A` cung cấp một lượng thông tin rất lớn và chi tiết về từng cổng. Chẳng hạn với cổng 21, kết quả còn bao gồm thông tin FTP server status và cho phép đăng nhập nặc danh (Anonymous FTP login allowed).

### 3.10. Kiểm tra thông tin SMB bằng Nmap Scripting Engine (NSE)
- **Mục đích:** Chạy thử một script NSE để lấy thông tin cụ thể về dịch vụ SMB.
- **Lệnh thực hiện:** `nmap --script smb-os-discovery 192.168.253.134`
- **Nhận xét:** Script đã phát hiện thành công hệ điều hành Unix (Samba 3.0.20-Debian) và tên máy tính (Computer name) là metasploitable.

### 3.11. Xuất kết quả quét
- **Mục đích:** Lưu lại toàn bộ kết quả quét ra file để làm minh chứng và báo cáo.
- **Lệnh thực hiện:** `nmap -sV 192.168.253.134 -oA lab4_nmap`
- **Nhận xét:** Nmap đã tạo ra 3 file kết quả với đuôi .gnmap, .nmap, và .xml để lưu trữ chi tiết lần quét.

## 4. Bảng tổng hợp kết quả cổng mở
Dựa trên kết quả SYN Scan và Service Detection, một số cổng mở tiêu biểu trên máy đích bao gồm:

| Cổng (Port) | Giao thức | Dịch vụ | Phiên bản dịch vụ |
|-------------|-----------|---------|-------------------|
| 21          | TCP       | ftp     | vsftpd 2.3.4      |
| 22          | TCP       | ssh     | OpenSSH 4.7p1     |
| 23          | TCP       | telnet  | Linux telnetd     |
| 80          | TCP       | http    | Apache httpd 2.2.8|
| 445         | TCP       | netbios-ssn | Samba smb 3.X - 4.X |

## 5. Nhận xét và đánh giá
Qua bài thực hành, em đã áp dụng thành công công cụ Nmap để quét và đánh giá bề mặt mạng của máy ảo Metasploitable 2. Nmap giúp phát hiện chính xác trạng thái máy (up/down), liệt kê chi tiết các cổng đang mở (open), cũng như nhận diện hệ điều hành và phiên bản dịch vụ đang chạy.

Việc xác định cổng và dịch vụ đóng vai trò vô cùng quan trọng trong quy trình an toàn thông tin. Nó giúp người quản trị (Blue Team) biết được những dịch vụ nào đang phơi bày ra mạng, từ đó xác định các lỗ hổng tiềm ẩn (ví dụ: chạy phiên bản dịch vụ quá cũ, hoặc mở các cổng không cần thiết). Đồng thời, việc nắm vững Nmap cũng cần tuân thủ nguyên tắc an toàn, chỉ thực hiện quét trên mạng nội bộ hoặc hệ thống được cho phép.

## 6. Câu hỏi phân tích

**Câu 1: Trạng thái open, closed và filtered khác nhau như thế nào?**  
**Trả lời:** "Open" nghĩa là có một ứng dụng trên máy đích đang lắng nghe các kết nối/gói tin trên cổng đó. "Closed" nghĩa là cổng không có ứng dụng nào lắng nghe, nhưng máy đích vẫn phản hồi lại. "Filtered" nghĩa là Nmap không thể xác định cổng mở hay đóng vì gói tin bị tường lửa hoặc bộ lọc mạng chặn lại.

**Câu 2: -sT (TCP Connect scan) và -sS (SYN scan) khác nhau như thế nào?**  
**Trả lời:** `-sT` thực hiện quá trình bắt tay 3 bước (3-way handshake) hoàn chỉnh, dễ bị hệ thống đích ghi log lại. Trong khi đó, `-sS` (Half-open scan) chỉ gửi gói SYN và khi nhận được SYN-ACK sẽ gửi RST để ngắt ngay lập tức, giúp quét nhanh hơn và ít bị ghi log hơn (cần quyền sudo/root).

**Câu 3: -sV dùng để làm gì?**  
**Trả lời:** Tùy chọn `-sV` dùng để dò tìm và xác định phiên bản chính xác của phần mềm hoặc dịch vụ đang chạy trên cổng đang mở, giúp phát hiện các dịch vụ lỗi thời dễ bị tấn công.

## 7. Kết luận
Bài lab đã giúp em nắm vững các kỹ thuật khảo sát host, cổng và dịch vụ thực tế bằng Nmap. Kết quả thu được từ máy đích Metasploitable 2 rất đa dạng và rõ ràng. Toàn bộ các thao tác này chỉ được thực hiện trong môi trường máy ảo cục bộ (Host-Only) nhằm mục đích học tập và không được sử dụng để quét các mục tiêu không được phép trên Internet.
