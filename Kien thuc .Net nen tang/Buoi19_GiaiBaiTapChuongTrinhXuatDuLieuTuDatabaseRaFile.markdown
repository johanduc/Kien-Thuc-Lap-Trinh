# Buổi 19: Giải bài tập - Chương trình xuất dữ liệu từ database ra file

## Mục tiêu bài học
- Hiểu cách xuất dữ liệu từ database ra file text sử dụng .NET.
- Làm quen với các thao tác đọc dữ liệu từ SQL Server và ghi vào file.
- Áp dụng kiến thức về ADO.NET và xử lý file trong C#.

## Nội dung chính

### 1. Đề bài
Viết chương trình xuất dữ liệu từ bảng `NhanVien` trong database ra một file text. File text sẽ chứa các thông tin của nhân viên với định dạng cụ thể.

**Yêu cầu:**
- Database: Sử dụng SQL Server với bảng `NhanVien` có các cột:
  - `MaNV` (mã nhân viên, kiểu int)
  - `HoTen` (họ tên, kiểu nvarchar)
  - `NgaySinh` (ngày sinh, kiểu date)
  - `Luong` (lương, kiểu float)
- File output: Dữ liệu được xuất ra file text (ví dụ: `NhanVien.txt`) với định dạng:
  ```
  MaNV   HoTen                    NgaySinh       Luong
  1      Nguyen Van A             1990-01-01     5000.0
  2      Tran Thi B               1995-02-15     6000.0
  ...
  ```
- Sử dụng ADO.NET để kết nối và truy vấn database.
- Sử dụng StreamWriter để ghi dữ liệu ra file.

### 2. Các bước thực hiện

#### Bước 1: Chuẩn bị database
- Tạo database `QuanLyNhanVien` trên SQL Server.
- Tạo bảng `NhanVien` với cấu trúc:
  ```sql
  CREATE TABLE NhanVien (
      MaNV INT PRIMARY KEY,
      HoTen NVARCHAR(50),
      NgaySinh DATE,
      Luong FLOAT
  );
  ```
- Thêm dữ liệu mẫu:
  ```sql
  INSERT INTO NhanVien VALUES
  (1, N'Nguyen Van A', '1990-01-01', 5000.0),
  (2, N'Tran Thi B', '1995-02-15', 6000.0),
  (3, N'Le Van C', '1988-03-20', 5500.0);
  ```

#### Bước 2: Tạo dự án C#
- Mở Visual Studio, tạo dự án Console Application (.NET Framework hoặc .NET Core).
- Thêm thư viện `System.Data.SqlClient` để làm việc với SQL Server.

#### Bước 3: Viết code kết nối và truy vấn database
- Sử dụng `SqlConnection` để kết nối tới database.
- Sử dụng `SqlCommand` và `SqlDataReader` để đọc dữ liệu từ bảng `NhanVien`.

#### Bước 4: Ghi dữ liệu ra file text
- Sử dụng `StreamWriter` để tạo và ghi dữ liệu vào file `NhanVien.txt`.
- Định dạng dữ liệu theo yêu cầu (căn lề các cột).

### 3. Code ví dụ

```csharp
using System;
using System.Data.SqlClient;
using System.IO;

namespace XuatDuLieuTuDatabase
{
    class Program
    {
        static void Main(string[] args)
        {
            // Chuỗi kết nối tới database
            string connectionString = @"Data Source=.\SQLEXPRESS;Initial Catalog=QuanLyNhanVien;Integrated Security=True";

            // Đường dẫn file output
            string filePath = "NhanVien.txt";

            try
            {
                // Tạo kết nối tới database
                using (SqlConnection connection = new SqlConnection(connectionString))
                {
                    connection.Open();

                    // Truy vấn lấy dữ liệu
                    string query = "SELECT MaNV, HoTen, NgaySinh, Luong FROM NhanVien";
                    SqlCommand command = new SqlCommand(query, connection);

                    // Đọc dữ liệu
                    SqlDataReader reader = command.ExecuteReader();

                    // Ghi dữ liệu ra file
                    using (StreamWriter writer = new StreamWriter(filePath))
                    {
                        // Ghi tiêu đề
                        writer.WriteLine("{0,-10} {1,-20} {2,-15} {3,-10}", "MaNV", "HoTen", "NgaySinh", "Luong");

                        // Ghi từng dòng dữ liệu
                        while (reader.Read())
                        {
                            int maNV = reader.GetInt32(0);
                            string hoTen = reader.GetString(1);
                            DateTime ngaySinh = reader.GetDateTime(2);
                            double luong = reader.GetDouble(3);

                            writer.WriteLine("{0,-10} {1,-20} {2,-15:yyyy-MM-dd} {3,-10}", maNV, hoTen, ngaySinh, luong);
                        }
                    }

                    Console.WriteLine("Xuất dữ liệu thành công!");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Lỗi: " + ex.Message);
            }

            Console.ReadKey();
        }
    }
}
```

### 4. Giải thích code

- **Chuỗi kết nối (`connectionString`):** Xác định thông tin kết nối tới SQL Server (tên server, tên database, chế độ xác thực).
- **SqlConnection:** Tạo đối tượng kết nối tới database. Sử dụng `using` để đảm bảo giải phóng tài nguyên sau khi sử dụng.
- **SqlCommand:** Thực thi câu lệnh SQL (`SELECT`) để lấy dữ liệu từ bảng `NhanVien`.
- **SqlDataReader:** Đọc từng dòng dữ liệu từ kết quả truy vấn.
- **StreamWriter:** Ghi dữ liệu ra file text. Sử dụng định dạng `{0,-10}` để căn lề trái với độ rộng cố định.
- **Xử lý ngoại lệ:** Bọc code trong khối `try-catch` để xử lý lỗi (ví dụ: lỗi kết nối database, lỗi ghi file).

### 5. Kết quả đầu ra
File `NhanVien.txt` sẽ có nội dung như sau:
```
MaNV       HoTen                NgaySinh        Luong     
1          Nguyen Van A         1990-01-01      5000      
2          Tran Thi B           1995-02-15      6000      
3          Le Van C             1988-03-20      5500      
```

### 6. Lưu ý khi thực hiện
- Đảm bảo SQL Server đang chạy và chuỗi kết nối đúng với cấu hình máy tính.
- Kiểm tra quyền ghi file tại thư mục chứa file `NhanVien.txt`.
- Xử lý các trường hợp dữ liệu đặc biệt (ví dụ: họ tên chứa ký tự đặc biệt, lương có giá trị âm).

### 7. Bài tập mở rộng
1. Thêm chức năng cho phép người dùng nhập đường dẫn file output từ bàn phím.
2. Xuất dữ liệu ra file CSV thay vì file text thông thường.
3. Thêm kiểm tra xem file output đã tồn tại hay chưa, nếu tồn tại thì hỏi người dùng có muốn ghi đè không.

## Tài liệu tham khảo
- ADO.NET: https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/
- StreamWriter: https://docs.microsoft.com/en-us/dotnet/api/system.io.streamwriter
- SQL Server Connection Strings: https://www.connectionstrings.com/sql-server/