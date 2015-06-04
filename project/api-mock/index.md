---
layout: project_overview
title: API MOCK Site
project: api-mock
---

api-mock là site viết bằng PHP, MySQL để tạo các web service giả.
Giúp việc phát triển ứng dụng không phụ thuộc vào quá nhiều vào service api thật(do service này có thể chưa làm xong, hay còn nhiều bug). 

api-mock có khả năng tạo nhiều response ứng với mỗi api service, mỗi response có thể là static content hay động tuy theo request parameter hay redirect sang server thật, ngoài ra cùng với khả năng ghi log lại request và response, api-mock đã làm việc debug dể dàng hơn.

![cover-image](/res/api-mock/cover-image.png)

## Tính năng của api-mock

* mỗi api có thể có nhiều response
* mỗi response có thể là dạng
    * static content
    * content động dựa trên request parameter(code xữ lí logic viết bằng syntax PHP)
    * redirect sang server thật
* log tất cả các content request, response của api

## Cách cài đặt

1. clone source mới nhất
2. tạo database
3. import structure database từ file ```db/api_mock_structure.sql```
4. import data khởi tạo từ file ```db/api_mock_data.sql```
5. vào file ```protected/config/main.php``` để cập nhật lại thông tin kết nối database
6. kết nối vào site vừa tạo, dùng account usr/pwd: admin/admin để login và sử dụng

## Yêu cầu 

1. Apache
2. PHP (curl đã enable)
3. MySQL
