# Giải pháp tường lửa đa tầng CrowdSec dựa trên phân tích hành vi
_Chứng minh tính hiệu quả của cơ chế phát hiện và ngăn chặn IP độc hại dựa trên phân tích hành vi của CrowdSec áp dụng trong bảo vệ hệ thống khỏi các cuộc tấn công từ trong/ngoài tổ chức._ 

## 1. Yêu cầu hệ thống
Thực nghiệm được triển khai hoàn toàn trên môi trường ảo hóa VMware có kết nối mạng.
* Yêu cầu phần cứng: RAM 8GB, SSD 60GB trống.
* Yêu cầu phần mềm: Máy cục bộ đã cài VMware, có kết nối mạng internet.
* 1 máy kali dùng để tấn công, đã cài hydra, wpscan, gobuster, nmap.
* 2 máy Ubuntu cần bảo vệ trong đó: 1 máy ubuntu server có dịch vụ Nginx cài CrowdSec để phân tích hành vi và tự bảo vệ bản thân , 1 máy ubuntu cài apache sẽ bouncers–firewall đến máy LAPI. 
* 1 máy vật lý chạy windows để quản lý tập trung CrowdSec trên giao diện Web. 

## 2. Mô hình triển khai
<img width="951" height="501" alt="image" src="https://github.com/user-attachments/assets/12ee048c-7aff-4e8f-ba63-3020187714ec" />

## 3. Setup môi trường
### Bước 1: Thiết lập các máy ảo
### Bước 2: Cài máy chủ web apache/nginx dịch vụ ssh lên các máy ảo
### Bước 3: Cài đặt CrowdSec và các gói tiện ích đi kèm
<img width="975" height="185" alt="image" src="https://github.com/user-attachments/assets/d39ecfb4-3dee-4b18-8d3e-4b9860ba1c8e" />

<img width="823" height="145" alt="image" src="https://github.com/user-attachments/assets/b92d4dbe-0234-4a46-b674-8ab4aadb3bd1" />

<img width="958" height="202" alt="image" src="https://github.com/user-attachments/assets/f612938b-98b5-47fb-9894-ae8f7703a446" />

<img width="975" height="327" alt="image" src="https://github.com/user-attachments/assets/35ffb950-ef80-41fb-b3bc-b17486181622" />

Các lệnh sẽ sử dụng:
* `sudo cscli bouncers list `                 //danh sách các máy đã kết nối 
* `sudo cscli bouncers add <tên tùy ý> `      //tạo api key cho bouncers
* `sudo cscli bouncers delete <tên>    `       //xóa các kết nối bouncer
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
### Bước 4: Cấu hình để CrowdSec kết nối đến các máy cùng mạng cần bảo vệ
<img width="975" height="583" alt="image" src="https://github.com/user-attachments/assets/8667298d-d9f5-4924-85f9-166c7f4febc8" />

<img width="975" height="66" alt="image" src="https://github.com/user-attachments/assets/d81c8195-b453-4b36-b26a-668cde0a6517" />

<img width="975" height="234" alt="image" src="https://github.com/user-attachments/assets/66e5ccfc-2f74-4f9f-be3f-8f7077d28a81" />

<img width="923" height="486" alt="image" src="https://github.com/user-attachments/assets/eabedb11-45e6-4922-bcb4-7618b23eba9e" />

### Bước 5: Kiểm tra kết quả
Kiểm tra lại bên máy server ubuntu, thấy IP của máy cần bảo vệ đã hiện lên ở bảng bouncers list
<img width="975" height="168" alt="image" src="https://github.com/user-attachments/assets/0198b3b9-2706-43c9-b528-20975222c694" />

## 4. Thực nghiệm
### Kịch bản 1: Tấn công dò quét hệ thống
1. SSH bruteforce bằng Hydra
* Từ máy kali thực hiện tấn công vào dịch vụ SSH trên máy chủ web ubuntu
`hydra -L username.txt -P test-password.txt -F ssh://192.168.2.134 -V -t 1`
<img width="998" height="419" alt="image" src="https://github.com/user-attachments/assets/43fcff54-b835-4c62-b229-26e2ee1a5418" />

* Mục đích là để kiểm tra khả năng phát hiện và ngăn chặn của crowdsec trên tấn công bruteforce
2. Dò quét CMS Fingerprinting bằng wpscan
* Cuộc tấn công này nhằm dò tìm và xác định website trên máy chủ đang dùng CMS (content management system) nào, phiên bản, theme, plugin từ đó chọn cách khai thác lỗ hổng phù hợp, trong scenarios đã có 2 kịch bản nhận diện đây là hành vi crawl bất thường. Do nhiều request được gửi đến trong thời gian ngắn và trỏ vào path đặc trưng của CMS từ cùng một IP.
<img width="975" height="519" alt="image" src="https://github.com/user-attachments/assets/b6f55b9c-38db-4d88-a171-37d21b656f3c" />

### Kịch bản 2: Tấn công truy cập và thu thập đường dẫn nhạy cảm bằng gobuster
* Thực hiện viết một scripts tấn công bằng tool gobuster chạy trên máy kali linux để tấn công máy chủ web. Đoạn scripts sử dụng chế độ liệt kê thư mục và tệp tin của địa chỉ 192.168.2.4, sử dụng file từ điển common.txt để thử tên của các thư mục và tệp tin phổ biến. Chạy 10 luồn song song để tăng tốc độ quét, và tìm kiếm các file cụ thể như .php, .txt, .bak, .zip, .old, .env
<img width="975" height="1076" alt="image" src="https://github.com/user-attachments/assets/7169e2e2-fdba-4433-8263-e12aebdf4439" />

### Kịch bản 3: Triển khai Community Blocklist, Scenarios cộng đồng để tự động bảo vệ hệ thống
* Với phiên bản CrowdSec mới nhất, Community Blocklist đã mặc định tích hợp sẵn và được tải về cùng khi ta install CrowdSec. Kiểm tra trạng thái của CAPI.
<img width="975" height="260" alt="image" src="https://github.com/user-attachments/assets/08fbb277-b433-4679-99d2-f797d62f9e43" />

* Các kịch bản phân tích hành vi do cộng đồng tạo ra thì ta cần tải về thủ công. Sử dụng lệnh dưới:
<img width="975" height="208" alt="image" src="https://github.com/user-attachments/assets/7ff37f58-1670-4e71-874b-eef6b426cd82" />

<img width="975" height="488" alt="image" src="https://github.com/user-attachments/assets/378da33a-23e5-40d4-b7c3-b6dfefd6c819" />

* Bên trên là một danh sách dài các IP mà ta chưa hề bị chúng tấn công, nhưng hệ thống vẫn chặn để phòng ngừa. 

## 5. Kết quả
* Kết quả các kịch bản, và phân tích sâu lý do CrowdSec có thể nhận biết và chặn được các hành vi ngay tức thì là do bộ kịch bản hành vi nằm bên trong đã được trình bày chi tiết trong báo cáo nghiên cứu .docx trên repo này.
* Các kịch bản tấn công thử nghiệm khả năng phân tích hành vi và phát hiện của CrowdSec đều thành công, tốc độ lan truyền quyền tới các máy trong bouncers cũng rất nhanh. Đây là một công cụ mã nguồn mở rất đáng để nghiên cứu và phát triển sâu hơn. Ngoài ra tính năng machine learning vẫn chưa được đánh giá và sử dụng nhưng cũng dự đoán sẽ khá tiềm năng.
* Nhược điểm là khả năng hỗ trợ trên các công cụ và hệ điều hành chưa đồng đều, qua quá trình triển khai thử nghiệm nhiều lần tôi thấy được việc triển khai trên các hệ điều hành nhân linux dễ dàng, tối ưu và nhanh chóng hơn so với windows. Ở thời điểm hiện tại Crowdsec chỉ có thể triển khai phân tích quản lí trên Linux, còn Windows nên được triển khai như một thiết bị được bảo vệ qua bouncer, đảm nhận vai trò nhận lệnh và thực thi quyết định từ crowdsec. 


















