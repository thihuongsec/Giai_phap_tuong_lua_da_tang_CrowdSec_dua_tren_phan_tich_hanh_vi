# Giải pháp tường lửa đa tầng CrowdSec dựa trên phân tích hành vi
Chứng minh tính hiệu quả của cơ chế phát hiện và ngăn chặn IP độc hại dựa trên phân tích hành vi của CrowdSec áp dụng trong bảo vệ hệ thống khỏi các cuộc tấn công từ trong/ngoài tổ chức. 

## 1. Yêu cầu hệ thống
Thực nghiệm được triển khai hoàn toàn trên môi trường ảo hóa VMware có kết nối mạng.
* Yêu cầu phần cứng: RAM 8GB, SSD 60GB trống.
* Yêu cầu phần mềm: Máy cục bộ đã cài VMware, có kết nối mạng internet.
* 1 máy kali dùng để tấn công, đã cài hydra, wpscan, gobuster, nmap.
* 2 máy Ubuntu cần bảo vệ trong đó: 1 máy ubuntu server có dịch vụ Nginx cài CrowdSec để phân tích hành vi và tự bảo vệ bản thân , 1 máy ubuntu cài apache sẽ bouncers–firewall đến máy LAPI. 
* 1 máy vật lý chạy windows để quản lý tập trung CrowdSec trên giao diện Web. 

## 2. Mô hình triển khai
<img width="951" height="501" alt="image" src="https://github.com/user-attachments/assets/12ee048c-7aff-4e8f-ba63-3020187714ec" />
* Bước 1: Thiết lập các máy ảo
* Bước 2: Cài máy chủ web apache/nginx dịch vụ ssh lên các máy ảo
* Bước 3: Cài đặt CrowdSec và các gói tiện ích đi kèm
<img width="975" height="185" alt="image" src="https://github.com/user-attachments/assets/d39ecfb4-3dee-4b18-8d3e-4b9860ba1c8e" />

<img width="823" height="145" alt="image" src="https://github.com/user-attachments/assets/b92d4dbe-0234-4a46-b674-8ab4aadb3bd1" />

Các lệnh sẽ sử dụng:
* `sudo cscli bouncers list `                 //danh sách các máy đã kết nối 
* `sudo cscli bouncers add <tên tùy ý> `      //tạo api key cho bouncers
*` sudo cscli bouncers delete <tên>    `       //xóa các kết nối bouncer
* `sudo cscli machines list   `             //danh sách thiết bị crowdsec lấy logs
* `sudo cscli machines add <tên tùy ý>  `     //tạo key api machines
* `sudo cscli machines delete <tên>  `        //xóa key api machines
* `sudo cscli collections list     `        //danh sách các gói logs được crowdsec nhận diện
* `sudo cscli metrics   `                       //các bảng dữ liệu thống kê của crowdsec
* `sudo cscli decisions list     `                    //danh sách các IP bị bạn
* `sudo cscli decisions delete -i <IP>`             //xóa IP khỏi list chặn
* `sudo cscli decisions add -i <IP>  `                //thêm IP vào list chặn
* `sudo cscli alerts list --limit 10  `          //list 10 cảnh báo gửi về
* `sudo cscli scenarios list`                 //danh sách kịch bản hành vi đang có



















