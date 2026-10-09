<img width="296" height="72" alt="image" src="https://github.com/user-attachments/assets/29ec4a23-c5a9-4bb7-9bfd-3666fb3a6be7" /># 1. LAB

## 1. Level 1 ##

Ở request GET xem thông tin bài viết, có param id, ta sẽ butefore giá trị id để xem lần lượt các bài viết dựa trên id

<img width="511" height="164" alt="image" src="https://github.com/user-attachments/assets/be83ecb5-f7b6-45d2-9be3-0767ead68fb3" />

<img width="790" height="227" alt="image" src="https://github.com/user-attachments/assets/b82c852c-f80f-4584-ad9a-5d78965d7722" />

`CBJS{05c12a287334386c94131ab8aa00d08a}`

## 2. Level 2 ##

Thử đăng bài ở 2 tài khoản khác nhau , ta thấy id bài viết có dạng `MDAwMDA` + số tăng dần theo thứ tự

<img width="292" height="27" alt="image" src="https://github.com/user-attachments/assets/175d1c25-537e-4afe-9d53-a9dca64021b0" />

<img width="295" height="29" alt="image" src="https://github.com/user-attachments/assets/42a2a577-2b76-48b8-ab2e-1a7e8446685d" />

Nhưng khi ta bruteforce lại không ra được kết quả ta cần --> ta sẽ thử decode cả cụm xem có ý nghĩa gì không

<img width="389" height="61" alt="image" src="https://github.com/user-attachments/assets/014998c4-e64c-40cc-ad69-66a2219b0ddf" />

```
function generate_id()
{
    $data = select_one('SELECT num_posts FROM counters');
    $current_idx = sprintf('%06d', $data['num_posts'] + 1);
    return base64_encode($current_idx);
}
```
So sánh với source code, vậy id bài viết sẽ bằng đọc num_posts từ dbs rồi cộng 1, định dạng thành 6 chữ số và mã hóa base64 ---> trả về ID 

Ta thử từ num_post 3, tức MDAwMDA0 thì khong ra flag, ta sẽ dò ngược lại những post trước đó

Encode `000003` --> MDAwMDAz --> CBJS{8016cad17ca68a50623756407a66a77b}

<img width="296" height="72" alt="image" src="https://github.com/user-attachments/assets/630b30fe-343f-4ae0-8124-34a40c16e0f9" />

## 3. Level 3 ##

Request GET này đọc content của bài viết nhưng id ở đây lại là đoạn mã random

<img width="516" height="124" alt="image" src="https://github.com/user-attachments/assets/ba63dc2f-a6fd-4147-ae22-df7d553d7bd6" />

khi nhấp vào wall crush, ta thấy request GET hiện lên id bài viết của user

<img width="615" height="128" alt="image" src="https://github.com/user-attachments/assets/97359095-8c53-4f13-a69b-56dea7b59fdf" />

Ta được flag CBJS{8c46c7271a47dd46a439ea6c25c8a92d}

<img width="619" height="122" alt="image" src="https://github.com/user-attachments/assets/d9a58061-c7be-4983-ac41-2d0b5f9c8292" />

## 4. Level 4 ##








# 2. NOTE
