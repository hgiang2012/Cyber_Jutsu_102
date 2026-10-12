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

a) TÌM FLAG
GET post_id của 1 bài private, ta không xem được nội dung trong đó

<img width="757" height="128" alt="image" src="https://github.com/user-attachments/assets/652dbdf9-22ff-4028-a4c4-08f2e987d2d6" />

Nhìn vào source code 

```
case 'read':
        $post = select_one(
            'SELECT content, public, author_id FROM posts
            WHERE post_id = ? AND (public = 1 OR author_id = ?)', 
            $_GET['id'],
            $user_id
        );
        if ($post)
            echo json_encode($post);
        else
            echo json_encode("Not Found");
        break;
```

Ta thấy có những trường hợp sau đây

| Trạng thái người dùng | author_id có khớp $user_id? | Kết quả |
| --- | --- | --- |
| Đã đăng nhập | Có | Đọc được |
| Đã đăng nhập | Không | Không đọc được |
| Chưa đăng nhập, $user_id = NULL | Không thể khớp với ID thông thường | Không đọc được |
| Chưa đăng nhập, $user_id = ? | Có bản ghi author_id = ? | Có thể đọc được |

Ta thử trường hợp cuối, gán thêm author_id vào param

`GET /post.php?action=read&id=3daccd4b87a67204a0dbf13d05fac328&user_id=2 `

Ta được `{"content":"Thich nhat may anh hacker <3 CBJS{d1aafc789e70e9492849e5c752961ca0} <3","public":"0","author_id":"2"}`

Trường hợp này xảy ra là vì 

```
WHERE post_id = '3daccd4b87a67204a0dbf13d05fac328'
  AND (public = 1 OR author_id = 2)
```

- `public = 1` --> FALSE vì bài viết đang ẩn
- `author_id = 2` --> TRUE vì tác giả có ID là 2
- `FALSE OR TRUE` --> TRUE
--> Query trả về bài viết ẩn vì hệ thống coi người yêu cầu là tác giả của bài viết

Đây là lỗ hổng Broken Access Control vì ứng dụng đã khiến user_id mang giá trị 2 mà không cần đăng 

b, TÌM LỖI ẨN

Tương tự, ta thử add user_id vào sau request POST đăng bài

<img width="383" height="260" alt="image" src="https://github.com/user-attachments/assets/6e8ff92c-2e19-4f0f-ba69-b1f4f99964a9" />

Thành công up bài lên user_id 2

<img width="959" height="244" alt="image" src="https://github.com/user-attachments/assets/ff48460b-53c3-415c-926d-848faf09373f" />

## 5. Level 5 ##

Ta thấy ở đây có 1 đường dãn lạ

<img width="437" height="358" alt="image" src="https://github.com/user-attachments/assets/a5a52463-ceb9-433d-8424-e4e242b0bd3e" />

Truy cập thử thì đây là trang của admin để push noti --> lỗi Access Control vì id người dùng thường không được có quyền truy cập cũng như push noti


<img width="785" height="254" alt="image" src="https://github.com/user-attachments/assets/bae50c23-8fe0-412c-9008-f7b321887d8f" />

<img width="120" height="26" alt="image" src="https://github.com/user-attachments/assets/9c0d9c9d-41ad-4bff-8017-518f173685d1" />


# 2. NOTE
