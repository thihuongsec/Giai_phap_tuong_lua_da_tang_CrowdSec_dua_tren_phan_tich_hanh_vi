# Giải pháp tường lửa đa tầng CrowdSec dựa trên phân tích hành vi
Chứng minh tính hiệu quả của cơ chế phát hiện và ngăn chặn IP độc hại dựa trên phân tích hành vi của CrowdSec áp dụng trong bảo vệ hệ thống khỏi các cuộc tấn công từ trong/ngoài tổ chức. 

## 1. Yêu cầu hệ thống
* Yêu cầu phần cứng: RAM 8GB, SSD 60GB trống.
* Yêu cầu phần mềm: Máy cục bộ đã cài VMware, có kết nối mạng internet.
Yêu cầu máy ảo: 
* 1 máy kali dùng để tấn công, đã cài hydra, wpscan, gobuster, nmap.
* 2 máy Ubuntu cần bảo vệ trong đó: 1 máy ubuntu server có dịch vụ Nginx cài CrowdSec để phân tích hành vi và tự bảo vệ bản thân , 1 máy ubuntu cài apache sẽ bouncers–firewall đến máy LAPI. 
* 1 máy vật lý chạy windows để quản lý tập trung CrowdSec trên giao diện Web. 
