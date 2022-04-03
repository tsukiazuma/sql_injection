### Người thực hiện: Trần Ngọc Nam
### Thời gian thực hiện: 03/04/2022

# Mục lục:
1. [SQLi là gì?](#1)
2. [Các trường hợp xảy ra SQLi](#2)
3. [Tác hại](#3)
4. [Khắc phục](#4)

## SQLi là gì?<a name="1"></a>
- SQL Injection là một kỹ thuật cho phép những kẻ tấn công lợi dụng lỗ hổng của việc kiểm tra dữ liệu đầu vào trong các ứng dụng web và các thông báo lỗi của hệ quản trị cơ sở dữ liệu trả về để thi hành các câu lệnh SQL bất hợp pháp.
- SQL injection có thể cho phép những kẻ tấn công thực hiện các thao tác trên cơ sở dữ liệu của ứng dụng, thậm chí là server mà ứng dụng đó đang chạy.
  
## Các trường hợp xảy ra SQLi:
- Không kiểm tra ký tự thoát truy vấn:
  - Đây là dạng lỗi SQL injection xảy ra khi thiếu đoạn mã kiểm tra dữ liệu đầu vào trong câu truy vấn SQL. Kết quả là người dùng cuối có thể thực hiện một số truy vấn không mong muốn đối với cơ sở dữ liệu của ứng dụng.
  - Ví dụ:
    ```php
        statement = "SELECT * FROM users WHERE name = '" + userName + "';"
    ```
  - Nếu userName được nhập theo kiểu ác ý như <code>a' or 't'='t</code>. Từ đó, kẻ tấn công có thể lấy được tất cả thông tin users vì t=t luôn đúng.
- Xử lý không đúng kiểu:
  - Lỗi SQL injection dạng này thường xảy ra đầu vào dữ liệu không rõ ràng hoặc thiếu bước kiểm tra và lọc kiểu dữ liệu đầu vào.
  - Ví dụ:
    ```php
        statement:= "SELECT * FROM data WHERE id = " + a_variable + ";"
    ```
  - Sau id sẽ thường là số để lấy được sản phẩm, nhưng kẻ tấn công sẽ nhập vào 1 chuỗi kí tự như <code>1;DROP TABLE users</code>. Lúc này, thay vì chỉ lấy ra sản phẩm, truy vấn sẽ đồng thời xóa table users trong database.
- Lỗi bảo mật bên trong máy chủ cơ sở dữ liệu:
  - Đôi khi lỗ hổng có thể tồn tại chính trong phần mềm máy chủ cơ sở dữ liệu, như là trường hợp hàm mysql_real_escape_string() của các máy chủ MySQL.

## Tác hại:<a name='3'></a>
- Vượt qua các khâu xác thực người dùng
- Chèn, xóa hoặc sửa đổi dữ liệu
- Đánh cắp các thông tin trong CSDL
- Chiếm quyền điều khiển hệ thống

## Khắc phục:<a name='4'></a>
- Xác thực đầu vào:
  - Quá trình xác thực nhằm xác minh xem loại input do người dùng gửi có hợp lệ hay không.
  - Ví dụ:
    - Đối với table:
        ```php
            switswitch ($tableName) {
                case 'data': return true;
                case 'users': return true;
                default: return new BadMessageException('Fail Table name');
            }
        ```
    - Đối với drop-list:
        ```php
            if(isset($_POST["id"]))
            {
                $number = $_POST["id"];
                if((is_numeric($number)) && ($number > 0) && ($number < 6))
                {
                    echo "Selected rating: " . $number;
                }
                else
                    echo "The rating has to be a number between 1 and 3!";
            }
        ```
- Tham số hóa truy vấn:
  - Các truy vấn tham số hóa là một phương tiện pre-compile lệnh SQL. Sau đó bạn có thể cung cấp các tham số để câu lệnh được thực thi.
  - Phương pháp này giúp Cơ sở dữ liệu có thể nhận ra mã và phân biệt nó với dữ liệu đầu vào.
  - Input của người dùng được trích dẫn tự động, và kiểu mã hóa này giúp giảm thiểu tấn công SQL injection.
  - Ví dụ:
    ```php
        String id = request.getParameter("id");
        String query = "SELECT * FROM cat WHERE id = ? ";
        PreparedStatement statement = connection.prepareStatement(query);
        statement.setString(1, id);
        ResultSet resultSet = statement.executeQuery();
    ```
- Sử dụng Escaping:
  - Dùng mysql_real_escape_string() trong PHP để tránh các ký tự có thể dẫn đến lệnh SQL không mong muốn.
  - Ví dụ:
    ```php
        $db_connection = mysqli_connect("localhost", "user", "password", "db");
		$username = mysqli_real_escape_string($db_connection, $_POST['username']);
		$password = mysqli_real_escape_string($db_connection, $_POST['password']);
		$query = "SELECT * FROM users WHERE username = '" . $username. "' AND password = '" . $password . "'"; 
    ```
- Giới hạn quyền truy cập của người dùng đối với cơ sở dữ liệu: Chỉ những tài khoản có quyền truy cập theo yêu cầu mới được kết nối với cơ sở dữ liệu.
hể gọi một stored procedure.