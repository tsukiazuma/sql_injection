### Người thực hiện: Trần Ngọc Nam
### Thời gian thực hiện: 29/03/2022

# MỤC LỤC:
1. [GIỚI THIỆU:](#1)
2. [CÁC TABLE CỦA INFORMATION_SCHEMA](#2)

# INFORMATION_SCHEMA<a name="1"></a>
- Là database nằm bên trong máy chủ MySQL, lưu thông tin về tất cả các database khác mà máy chủ MySQL đang lưu giữ.
- Chứa các table read-only. Chúng thực chất là các view, chứ không phải các table thực sự, do đó không có file nào liên kết với chúng, và chúng ta không thể đặt trigger lên các table này. Ngoài ra thì không có thư mục của database này trong máy chủ MySQL.
- Chỉ có lệnh SELECT là chạy được trên database INFORMATION_SCHEMA.
  ```php
    mysql> SELECT table_name, table_type, engine FROM information_schema.tables ORDER BY table_name LIMIT 10;

    Kết quả:
    +---------------------------------------+-------------+-------------------+
    | TABLE_NAME                            | TABLE_TYPE  | ENGINE            |
    +---------------------------------------+-------------+-------------------+
    | accounts                              | BASE TABLE  | PERFORMANCE_SCHEMA|
    | CHARACTER_SETS                        | SYSTEM VIEW | NULL              |
    | COLLATION_CHARACTER_SET_APPLICABILITY | SYSTEM VIEW | NULL              |
    | COLLATIONS                            | SYSTEM VIEW | NULL              |
    | COLUMN_PRIVILEGES                     | SYSTEM VIEW | NULL              |
    | COLUMN_STATISTICS                     | SYSTEM VIEW | NULL              |
    | COLUMNS                               | SYSTEM VIEW | NULL              |
    | columns_priv                          | BASE TABLE  | InnoDB            |
    | component                             | BASE TABLE  | InnoDB            |
    | cond_instances                        | BASE TABLE  | PERFORMANCE_SCHEMA|
    +---------------------------------------+-------------+-------------------+
    10 rows in set (0.01 sec)
    ```
- Các lệnh INSERT, UPDATE, DELETE sẽ không chạy được trên database này.
  ```php
    mysql> UPDATE information_schema.tables 
    -> SET ENGINE='Test_Engine' 
    -> WHERE table_name='CHARACTER_SETS';
    ERROR 1044 (42000): Access denied for user 'root'@'localhost' to database 'information_schema'
  ```

# Các table của INFORMATION_SCHEMA:<a name="2"></a>
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

- Ngoài các table trên, trong INFORMATION_SCHEMA còn có các table lưu trữ các thông tin dành riêng cho các table của InnoDB.