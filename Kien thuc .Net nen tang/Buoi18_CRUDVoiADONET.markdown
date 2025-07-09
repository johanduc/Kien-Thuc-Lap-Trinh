# Buổi 18: CRUD với ADO.NET

Dưới đây là ghi chép và tổng hợp đầy đủ các kiến thức, bài tập ví dụ từ video YouTube: [Học lập trình .NET - Phần 18 - CRUD với ADO.NET](https://www.youtube.com/watch?v=YANJFeZSKWw&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=18). Nội dung bao gồm lý thuyết, các bước thực hiện CRUD (Create, Read, Update, Delete) với ADO.NET và các ví dụ minh họa.

---

## 1. Giới thiệu về ADO.NET

### 1.1 ADO.NET là gì?
- **ADO.NET** (ActiveX Data Objects .NET) là một tập hợp các lớp trong .NET Framework được sử dụng để truy cập và thao tác với cơ sở dữ liệu (CSDL).
- Cung cấp các công cụ để kết nối, truy vấn và quản lý dữ liệu từ các nguồn như SQL Server, Oracle, MySQL, v.v.
- Hai thành phần chính:
  - **Data Provider**: Kết nối với CSDL (ví dụ: `SqlConnection`, `SqlCommand`, `SqlDataReader` cho SQL Server).
  - **DataSet**: Lưu trữ dữ liệu trong bộ nhớ, hỗ trợ làm việc với dữ liệu ngoại tuyến (offline).

### 1.2 Các thành phần chính của ADO.NET
- **Connection**: Kết nối đến CSDL (ví dụ: `SqlConnection`).
- **Command**: Thực thi câu lệnh SQL (ví dụ: `SqlCommand`).
- **DataReader**: Đọc dữ liệu từ CSDL theo kiểu chỉ tiến (forward-only, read-only).
- **DataAdapter**: Cầu nối giữa CSDL và `DataSet`, hỗ trợ lấy và cập nhật dữ liệu.
- **DataSet/DataTable**: Lưu trữ dữ liệu trong bộ nhớ để thao tác.

### 1.3 Các thao tác CRUD
- **Create**: Thêm mới dữ liệu vào CSDL.
- **Read**: Đọc dữ liệu từ CSDL.
- **Update**: Cập nhật dữ liệu trong CSDL.
- **Delete**: Xóa dữ liệu khỏi CSDL.

---

## 2. Thiết lập môi trường

### 2.1 Yêu cầu
- Visual Studio (bất kỳ phiên bản nào hỗ trợ .NET, ví dụ: Visual Studio 2022).
- SQL Server (hoặc SQL Server Express).
- Cơ sở dữ liệu mẫu (ví dụ: `QuanLySinhVien`).

### 2.2 Tạo cơ sở dữ liệu mẫu
Tạo một cơ sở dữ liệu `QuanLySinhVien` với bảng `SinhVien` có cấu trúc sau:

```sql
CREATE DATABASE QuanLySinhVien;
GO

USE QuanLySinhVien;
GO

CREATE TABLE SinhVien (
    MaSV INT PRIMARY KEY,
    HoTen NVARCHAR(50),
    NgaySinh DATE,
    GioiTinh BIT,
    DiaChi NVARCHAR(100)
);
GO
```

### 2.3 Chuỗi kết nối (Connection String)
Chuỗi kết nối đến SQL Server, ví dụ:

```csharp
string connectionString = "Server=.;Database=QuanLySinhVien;Integrated Security=True;";
```

- `Server`: Tên máy chủ SQL Server (`.` là localhost).
- `Database`: Tên CSDL.
- `Integrated Security`: Sử dụng xác thực Windows (hoặc thay bằng `User ID` và `Password` nếu cần).

---

## 3. Thực hiện CRUD với ADO.NET

### 3.1 Thêm mới (Create)
Sử dụng `SqlCommand` để thực thi câu lệnh `INSERT`.

**Ví dụ**: Thêm một sinh viên mới vào bảng `SinhVien`.

```csharp
using System;
using System.Data.SqlClient;

class Program
{
    static void Main(string[] args)
    {
        string connectionString = "Server=.;Database=QuanLySinhVien;Integrated Security=True;";
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            try
            {
                connection.Open();
                string sql = "INSERT INTO SinhVien (MaSV, HoTen, NgaySinh, GioiTinh, DiaChi) " +
                             "VALUES (@MaSV, @HoTen, @NgaySinh, @GioiTinh, @DiaChi)";

                using (SqlCommand command = new SqlCommand(sql, connection))
                {
                    command.Parameters.AddWithValue("@MaSV", 1);
                    command.Parameters.AddWithValue("@HoTen", "Nguyen Van A");
                    command.Parameters.AddWithValue("@NgaySinh", new DateTime(2000, 1, 1));
                    command.Parameters.AddWithValue("@GioiTinh", true);
                    command.Parameters.AddWithValue("@DiaChi", "Hanoi");

                    int rowsAffected = command.ExecuteNonQuery();
                    Console.WriteLine($"Đã thêm {rowsAffected} dòng.");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Lỗi: " + ex.Message);
            }
        }
    }
}
```

**Giải thích**:
- `SqlConnection`: Mở kết nối đến CSDL.
- `SqlCommand`: Tạo câu lệnh SQL với tham số để tránh SQL Injection.
- `ExecuteNonQuery`: Thực thi câu lệnh INSERT, trả về số dòng bị ảnh hưởng.

---

### 3.2 Đọc dữ liệu (Read)
Sử dụng `SqlDataReader` để đọc dữ liệu từ câu lệnh `SELECT`.

**Ví dụ**: Lấy danh sách tất cả sinh viên.

```csharp
using System;
using System.Data.SqlClient;

class Program
{
    static void Main(string[] args)
    {
        string connectionString = "Server=.;Database=QuanLySinhVien;Integrated Security=True;";
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            try
            {
                connection.Open();
                string sql = "SELECT * FROM SinhVien";
                using (SqlCommand command = new SqlCommand(sql, connection))
                {
                    using (SqlDataReader reader = command.ExecuteReader())
                    {
                        while (reader.Read())
                        {
                            Console.WriteLine($"Mã SV: {reader["MaSV"]}, " +
                                              $"Họ Tên: {reader["HoTen"]}, " +
                                              $"Ngày Sinh: {reader["NgaySinh"]:d}, " +
                                              $"Giới Tính: {(reader["GioiTinh"] is true ? "Nam" : "Nữ")}, " +
                                              $"Địa Chỉ: {reader["DiaChi"]}");
                        }
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Lỗi: " + ex.Message);
            }
        }
    }
}
```

**Giải thích**:
- `ExecuteReader`: Trả về `SqlDataReader` để đọc từng dòng dữ liệu.
- `reader.Read()`: Di chuyển con trỏ đến dòng tiếp theo.
- Dữ liệu được truy xuất qua tên cột hoặc chỉ số cột.

---

### 3.3 Cập nhật (Update)
Sử dụng `SqlCommand` để thực thi câu lệnh `UPDATE`.

**Ví dụ**: Cập nhật thông tin sinh viên có `MaSV = 1`.

```csharp
using System;
using System.Data.SqlClient;

class Program
{
    static void Main(string[] args)
    {
        string connectionString = "Server=.;Database=QuanLySinhVien;Integrated Security=True;";
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            try
            {
                connection.Open();
                string sql = "UPDATE SinaiVien SET HoTen = @HoTen, DiaChi = @DiaChi WHERE MaSV = @MaSV";
                using (SqlCommand command = new SqlCommand(sql, connection))
                {
                    command.Parameters.AddWithValue("@MaSV", 1);
                    command.Parameters.AddWithValue("@HoTen", "Nguyen Van B");
                    command.Parameters.AddWithValue("@DiaChi", "Ho Chi Minh");

                    int rowsAffected = command.ExecuteNonQuery();
                    Console.WriteLine($"Đã cập nhật {rowsAffected} dòng.");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Lỗi: " + ex.Message);
            }
        }
    }
}
```

**Giải thích**:
- Câu lệnh `UPDATE` sử dụng tham số để cập nhật dữ liệu.
- `ExecuteNonQuery` trả về số dòng bị ảnh hưởng.

---

### 3.4 Xóa (Delete)
Sử dụng `SqlCommand` để thực thi câu lệnh `DELETE`.

**Ví dụ**: Xóa sinh viên có `MaSV = 1`.

```csharp
using System;
using System.Data.SqlClient;

class Program
{
    static void Main(string[] args)
    {
        string connectionString = "Server=.;Database=QuanLySinhVien;Integrated Security=True;";
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            try
            {
                connection.Open();
                string sql = "DELETE FROM SinhVien WHERE MaSV = @MaSV";
                using (SqlCommand command = new SqlCommand(sql, connection))
                {
                    command.Parameters.AddWith reconstitution("@MaSV", 1);

                    int rowsAffected = command.ExecuteNonQuery();
                    Console.WriteLine($"Đã xóa {rowsAffected} dòng.");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Lỗi: " + ex.Message);
            }
        }
    }
}
```

**Giải thích**:
- Câu lệnh `DELETE` sử dụng tham số để xóa dữ liệu.
- `ExecuteNonQuery` trả về số dòng bị ảnh hưởng.

---

## 4. Sử dụng DataAdapter và DataSet

### 4.1 Giới thiệu
- `DataAdapter`: Cầu nối giữa CSDL và `DataSet`.
- `DataSet`: Lưu trữ dữ liệu trong bộ nhớ, hỗ trợ làm việc ngoại tuyến.
- Phù hợp khi cần thao tác với nhiều bảng hoặc dữ liệu phức tạp.

### 4.2 Ví dụ: Sử dụng DataAdapter để lấy và cập nhật dữ liệu

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

class Program
{
    static void Main(string[] args)
    {
        string connectionString = "Server=.;Database=QuanLySinhVien;Integrated Security=True;";
        using (SqlConnection connection = new SqlConnection(connectionString))
        {
            try
            {
                connection.Open();
                string sql = "SELECT * FROM SinhVien";
                using (SqlDataAdapter adapter = new SqlDataAdapter(sql, connection))
                {
                    DataSet dataSet = new DataSet();
                    adapter.Fill(dataSet, "SinhVien");

                    // Hiển thị dữ liệu
                    foreach (DataRow row in dataSet.Tables["SinhVien"].Rows)
                    {
                        Console.WriteLine($"Mã SV: {row["MaSV"]}, " +
                                          $"Họ Tên: {row["HoTen"]}, " +
                                          $"Địa Chỉ: {row["DiaChi"]}");
                    }

                    // Cập nhật dữ liệu
                    DataRow newRow = dataSet.Tables["SinhVien"].NewRow();
                    newRow["MaSV"] = 2;
                    newRow["HoTen"] = "Tran Thi C";
                    newRow["NgaySinh"] = new DateTime(2001, 2, 2);
                    newRow["GioiTinh"] = false;
                    newRow["DiaChi"] = "Da Nang";
                    dataSet.Tables["SinhVien"].Rows.Add(newRow);

                    // Cập nhật CSDL
                    using (SqlCommandBuilder builder = new SqlCommandBuilder(adapter))
                    {
                        adapter.Update(dataSet, "SinhVien");
                    }
                    Console.WriteLine("Đã cập nhật dữ liệu.");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Lỗi: " + ex.Message);
            }
        }
    }
}
```

**Giải thích**:
- `SqlDataAdapter.Fill`: Lấy dữ liệu từ CSDL và đổ vào `DataSet`.
- `SqlCommandBuilder`: Tự động tạo các câu lệnh `INSERT`, `UPDATE`, `DELETE` dựa trên câu lệnh `SELECT`.
- `adapter.Update`: Đồng bộ dữ liệu từ `DataSet` về CSDL.

---

## 5. Lưu ý khi làm việc với ADO.NET

- **Quản lý kết nối**: Luôn sử dụng `using` để đảm bảo đóng kết nối sau khi sử dụng.
- **Tránh SQL Injection**: Sử dụng tham số (`Parameters`) thay vì nối chuỗi SQL.
- **Xử lý ngoại lệ**: Bắt và xử lý lỗi bằng `try-catch`.
- **Hiệu suất**:
  - Sử dụng `SqlDataReader` cho các tác vụ đọc nhanh, chỉ tiến.
  - Sử dụng `DataSet` khi cần thao tác dữ liệu phức tạp hoặc ngoại tuyến.

---

## 6. Bài tập thực hành

1. **Bài tập 1**: Viết chương trình thực hiện các thao tác CRUD trên bảng `SinhVien` sử dụng ADO.NET:
   - Thêm 3 sinh viên mới.
   - Hiển thị danh sách sinh viên.
   - Cập nhật địa chỉ của sinh viên có `MaSV = 2`.
   - Xóa sinh viên có `MaSV = 3`.

2. **Bài tập 2**: Sử dụng `DataSet` và `SqlDataAdapter` để:
   - Lấy danh sách sinh viên từ bảng `SinhVien`.
   - Thêm một sinh viên mới vào `DataSet`.
   - Cập nhật thông tin một sinh viên trong `DataSet`.
   - Đồng bộ dữ liệu từ `DataSet` về CSDL.

3. **Bài tập 3**: Viết chương trình sử dụng tham số để tìm kiếm sinh viên theo `HoTen` (sử dụng `LIKE` trong SQL).

---

## 7. Kết luận

- ADO.NET là một công cụ mạnh mẽ để làm việc với cơ sở dữ liệu trong .NET.
- Các thao tác CRUD được thực hiện dễ dàng với `SqlConnection`, `SqlCommand`, `SqlDataReader`, và `SqlDataAdapter`.
- Sử dụng tham số để đảm bảo an toàn và hiệu suất.
- `DataSet` và `DataAdapter` phù hợp cho các ứng dụng cần làm việc với dữ liệu phức tạp hoặc ngoại tuyến.