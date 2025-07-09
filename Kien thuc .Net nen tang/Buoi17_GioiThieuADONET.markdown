# Buổi 17: Giới thiệu ADO.NET

## Mục tiêu
- Hiểu khái niệm ADO.NET và vai trò của nó trong lập trình .NET.
- Nắm được các thành phần chính của ADO.NET: Connection, Command, DataReader, DataAdapter, DataSet.
- Hiểu cách sử dụng ADO.NET để kết nối và thao tác với cơ sở dữ liệu SQL Server.
- Thực hành các ví dụ cơ bản về truy xuất và thao tác dữ liệu.

## Nội dung chính

### 1. ADO.NET là gì?
- **ADO.NET** (ActiveX Data Objects .NET) là một công nghệ của Microsoft dùng để truy cập và thao tác dữ liệu từ các nguồn dữ liệu khác nhau (chủ yếu là cơ sở dữ liệu như SQL Server, Oracle, MySQL, v.v.).
- ADO.NET cung cấp hai mô hình chính:
  - **Connected**: Làm việc trực tiếp với cơ sở dữ liệu qua kết nối liên tục (Connection, Command, DataReader).
  - **Disconnected**: Làm việc với dữ liệu được tải về bộ nhớ (DataSet, DataTable, DataAdapter).
- ADO.NET hỗ trợ hiệu suất cao, khả năng mở rộng và tính linh hoạt khi làm việc với dữ liệu.

### 2. Các thành phần chính của ADO.NET
#### a. Connection
- Đối tượng **Connection** (ví dụ: `SqlConnection`) được sử dụng để thiết lập kết nối tới cơ sở dữ liệu.
- Chuỗi kết nối (Connection String) chứa thông tin như:
  - Tên server
  - Tên cơ sở dữ liệu
  - Thông tin xác thực (username, password hoặc Integrated Security)
- Ví dụ chuỗi kết nối:
  ```plaintext
  Server=localhost;Database=QuanLySinhVien;Integrated Security=True;
  ```

#### b. Command
- Đối tượng **Command** (ví dụ: `SqlCommand`) dùng để thực thi các câu lệnh SQL (SELECT, INSERT, UPDATE, DELETE).
- Các loại Command:
  - **Text**: Câu lệnh SQL dạng văn bản.
  - **StoredProcedure**: Gọi stored procedure.
  - **TableDirect**: Lấy trực tiếp dữ liệu từ bảng.
- Command có thể chứa tham số (Parameters) để tránh SQL Injection.

#### c. DataReader
- **DataReader** (ví dụ: `SqlDataReader`) đọc dữ liệu từ cơ sở dữ liệu theo kiểu chỉ tiến (forward-only) và chỉ đọc (read-only).
- Ưu điểm: Nhanh, nhẹ, phù hợp cho việc đọc dữ liệu lớn.
- Nhược điểm: Yêu cầu kết nối liên tục.

#### d. DataAdapter
- **DataAdapter** (ví dụ: `SqlDataAdapter`) làm cầu nối giữa cơ sở dữ liệu và DataSet/DataTable.
- Dùng để nạp dữ liệu vào DataSet và cập nhật thay đổi từ DataSet về cơ sở dữ liệu.

#### e. DataSet
- **DataSet** là một bộ nhớ đệm trong RAM, chứa dữ liệu từ cơ sở dữ liệu dưới dạng các DataTable.
- Hỗ trợ mô hình Disconnected: Không cần kết nối liên tục với cơ sở dữ liệu.
- DataSet có thể chứa nhiều DataTable và mối quan hệ giữa chúng.

### 3. Quy trình làm việc với ADO.NET
#### a. Mô hình Connected
1. Tạo kết nối (`SqlConnection`).
2. Mở kết nối (`Open()`).
3. Tạo câu lệnh SQL (`SqlCommand`).
4. Thực thi câu lệnh (dùng `ExecuteReader` cho SELECT, `ExecuteNonQuery` cho INSERT/UPDATE/DELETE).
5. Đọc dữ liệu (nếu dùng `DataReader`).
6. Đóng kết nối (`Close()`).

#### b. Mô hình Disconnected
1. Tạo kết nối (`SqlConnection`).
2. Tạo `DataAdapter` với câu lệnh SQL.
3. Nạp dữ liệu vào `DataSet` hoặc `DataTable` (`Fill`).
4. Thao tác dữ liệu trên `DataSet`.
5. Cập nhật thay đổi về cơ sở dữ liệu (`Update`).

### 4. Ví dụ minh họa
#### a. Ví dụ 1: Đọc dữ liệu bằng DataReader (Connected)
- **Mục đích**: Lấy danh sách sinh viên từ bảng `SinhVien` trong cơ sở dữ liệu `QuanLySinhVien`.
- **Code**:
  ```csharp
  using System;
  using System.Data.SqlClient;

  class Program
  {
      static void Main(string[] args)
      {
          string connectionString = "Server=localhost;Database=QuanLySinhVien;Integrated Security=True;";
          using (SqlConnection connection = new SqlConnection(connectionString))
          {
              try
              {
                  connection.Open();
                  string sql = "SELECT MaSV, HoTen, NgaySinh FROM SinhVien";
                  SqlCommand command = new SqlCommand(sql, connection);
                  SqlDataReader reader = command.ExecuteReader();

                  while (reader.Read())
                  {
                      Console.WriteLine($"MaSV: {reader["MaSV"]}, HoTen: {reader["HoTen"]}, NgaySinh: {reader["NgaySinh"]}");
                  }
                  reader.Close();
              }
              catch (Exception ex)
              {
                  Console.WriteLine("Loi: " + ex.Message);
              }
          }
      }
  }
  ```

#### b. Ví dụ 2: Sử dụng DataAdapter và DataTable (Disconnected)
- **Mục đích**: Lấy dữ liệu sinh viên và hiển thị, sau đó thêm một sinh viên mới.
- **Code**:
  ```csharp
  using System;
  using System.Data;
  using System.Data.SqlClient;

  class Program
  {
      static void Main(string[] args)
      {
          string connectionString = "Server=localhost;Database=QuanLySinhVien;Integrated Security=True;";
          using (SqlConnection connection = new SqlConnection(connectionString))
          {
              try
              {
                  // Tạo DataAdapter
                  string sql = "SELECT MaSV, HoTen, NgaySinh FROM SinhVien";
                  SqlDataAdapter adapter = new SqlDataAdapter(sql, connection);
                  DataTable table = new DataTable();
                  adapter.Fill(table);

                  // Hiển thị dữ liệu
                  foreach (DataRow row in table.Rows)
                  {
                      Console.WriteLine($"MaSV: {row["MaSV"]}, HoTen: {row["HoTen"]}, NgaySinh: {row["NgaySinh"]}");
                  }

                  // Thêm sinh viên mới
                  DataRow newRow = table.NewRow();
                  newRow["MaSV"] = "SV006";
                  newRow["HoTen"] = "Nguyen Van F";
                  newRow["NgaySinh"] = DateTime.Parse("2000-06-15");
                  table.Rows.Add(newRow);

                  // Cập nhật cơ sở dữ liệu
                  SqlCommandBuilder builder = new SqlCommandBuilder(adapter);
                  adapter.Update(table);
                  Console.WriteLine("Them sinh vien thanh cong!");
              }
              catch (Exception ex)
              {
                  Console.WriteLine("Loi: " + ex.Message);
              }
          }
      }
  }
  ```

### 5. Một số lưu ý khi sử dụng ADO.NET
- **Quản lý kết nối**: Luôn đóng kết nối (`Close()`) hoặc sử dụng `using` để tự động giải phóng tài nguyên.
- **Xử lý ngoại lệ**: Sử dụng `try-catch` để xử lý lỗi khi làm việc với cơ sở dữ liệu.
- **SQL Injection**: Sử dụng tham số trong `SqlCommand` thay vì nối chuỗi SQL trực tiếp.
- **Hiệu suất**: Chọn mô hình Connected hoặc Disconnected tùy theo nhu cầu:
  - Connected: Phù hợp khi cần đọc dữ liệu lớn hoặc thực hiện truy vấn nhanh.
  - Disconnected: Phù hợp khi cần thao tác dữ liệu cục bộ mà không cần kết nối liên tục.

### 6. Bài tập thực hành
1. **Bài tập 1**: Viết chương trình sử dụng DataReader để lấy danh sách các sinh viên có ngày sinh trong năm 2000 từ bảng `SinhVien`.
2. **Bài tập 2**: Viết chương trình sử dụng DataAdapter để:
   - Lấy toàn bộ dữ liệu từ bảng `SinhVien`.
   - Thêm một sinh viên mới.
   - Xóa một sinh viên dựa trên `MaSV`.
   - Cập nhật họ tên của một sinh viên.
3. **Bài tập 3**: Tạo một stored procedure trong SQL Server để lấy danh sách sinh viên theo lớp (mã lớp là tham số đầu vào). Sử dụng ADO.NET để gọi stored procedure này và hiển thị kết quả.

### 7. Tài liệu tham khảo
- Tài liệu chính thức của Microsoft về ADO.NET: [ADO.NET Overview](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/)
- Video YouTube: [Học lập trình .NET - Phần 17 - Giới thiệu ADO.NET](https://www.youtube.com/watch?v=oJOk40gm984&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=17)