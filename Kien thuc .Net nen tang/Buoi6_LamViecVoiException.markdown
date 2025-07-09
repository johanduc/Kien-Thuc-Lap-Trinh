# Buổi 6: Làm việc với Exception trong .NET

## Mục tiêu

- Hiểu khái niệm Exception trong lập trình .NET.
- Nắm cách sử dụng khối `try-catch` để xử lý ngoại lệ.
- Tìm hiểu các loại Exception phổ biến.
- Thực hành các ví dụ về xử lý ngoại lệ trong C#.
- Hiểu cách ném và bắt ngoại lệ tùy chỉnh.

## Nội dung chính

### 1. Exception là gì?

- **Exception** là một sự kiện bất thường xảy ra trong quá trình thực thi chương trình, làm gián đoạn luồng xử lý bình thường.
- Trong .NET, các Exception được biểu diễn bằng các lớp kế thừa từ lớp `System.Exception`.
- Một số thông tin quan trọng của Exception:
  - `Message`: Mô tả lỗi.
  - `StackTrace`: Lịch sử các lời gọi hàm dẫn đến lỗi.
  - `InnerException`: Ngoại lệ bên trong (nếu có).

### 2. Cấu trúc xử lý Exception

- Sử dụng khối `try-catch` để bắt và xử lý ngoại lệ.
- Cú pháp cơ bản:
  ```csharp
  try
  {
      // Code có khả năng gây ra ngoại lệ
  }
  catch (ExceptionType ex)
  {
      // Xử lý ngoại lệ
  }
  finally
  {
      // Code luôn được thực thi, bất kể có ngoại lệ hay không
  }
  ```

### 3. Các loại Exception phổ biến

- `System.NullReferenceException`: Truy cập vào đối tượng null.
- `System.IndexOutOfRangeException`: Truy cập chỉ số ngoài phạm vi mảng.
- `System.FormatException`: Định dạng dữ liệu không hợp lệ.
- `System.IO.IOException`: Lỗi liên quan đến thao tác nhập/xuất.
- `System.DivideByZeroException`: Chia cho số 0.

### 4. Ví dụ thực tế

#### Ví dụ 1: Xử lý ngoại lệ khi chia cho số 0

```csharp
Console.WriteLine("Nhap so a: ");
int a = int.Parse(Console.ReadLine());
Console.WriteLine("Nhap so b: ");
int b = int.Parse(Console.ReadLine());

try
{
    int result = a / b;
    Console.WriteLine($"Ket qua: {result}");
}
catch (DivideByZeroException ex)
{
    Console.WriteLine("Loi: Khong the chia cho 0!");
    Console.WriteLine($"Chi tiet loi: {ex.Message}");
}
catch (FormatException ex)
{
    Console.WriteLine("Loi: Vui long nhap so hop le!");
    Console.WriteLine($"Chi tiet loi: {ex.Message}");
}
finally
{
    Console.WriteLine("Ket thuc chuong trinh.");
}
```

**Giải thích**:

- Khối `try` chứa code thực hiện phép chia.
- Khối `catch` bắt lỗi `DivideByZeroException` khi chia cho 0 và `FormatException` khi nhập không phải số.
- Khối `finally` luôn được thực thi, dù có lỗi hay không.

#### Ví dụ 2: Xử lý ngoại lệ khi truy cập mảng

```csharp
int[] numbers = { 1, 2, 3 };
Console.WriteLine("Nhap chi so: ");
int index = int.Parse(Console.ReadLine());

try
{
    Console.WriteLine($"Gia tri tai chi so {index}: {numbers[index]}");
}
catch (IndexOutOfRangeException ex)
{
    Console.WriteLine("Loi: Chi so vuot qua pham vi mang!");
    Console.WriteLine($"Chi tiet loi: {ex.Message}");
}
catch (FormatException ex)
{
    Console.WriteLine("Loi: Vui long nhap so hop le!");
    Console.WriteLine($"Chi tiet loi: {ex.Message}");
}
```

**Giải thích**:

- Nhập chỉ số từ người dùng và truy cập mảng.
- Bắt lỗi `IndexOutOfRangeException` khi chỉ số vượt quá giới hạn mảng.

#### Ví dụ 3: Tạo và ném ngoại lệ tùy chỉnh

```csharp
class CustomException : Exception
{
    public CustomException(string message) : base(message) { }
}

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Nhap tuoi: ");
        int age = int.Parse(Console.ReadLine());

        try
        {
            if (age < 0)
            {
                throw new CustomException("Tuoi khong the am!");
            }
            Console.WriteLine($"Tuoi cua ban: {age}");
        }
        catch (CustomException ex)
        {
            Console.WriteLine($"Loi: {ex.Message}");
        }
        catch (FormatException ex)
        {
            Console.WriteLine("Loi: Vui long nhap so hop le!");
        }
    }
}
```

**Giải thích**:

- Tạo lớp `CustomException` kế thừa từ `Exception`.
- Ném ngoại lệ tùy chỉnh bằng từ khóa `throw` khi tuổi âm.
- Bắt và xử lý ngoại lệ tùy chỉnh trong khối `catch`.

### 5. Best Practices khi làm việc với Exception

- **Sử dụng Exception cụ thể**: Bắt các loại Exception cụ thể thay vì chỉ dùng `Exception` chung chung.
- **Không l 吞 lặng Exception**: Tránh để khối `catch` trống, luôn ghi log hoặc xử lý lỗi.
- **Sử dụng finally**: Đảm bảo giải phóng tài nguyên (file, kết nối database,...) trong khối `finally`.
- **Tạo Exception tùy chỉnh**: Khi cần xử lý các lỗi đặc thù, tạo lớp Exception riêng.
- **Ghi log lỗi**: Sử dụng các công cụ như `NLog` hoặc `Serilog` để ghi lại chi tiết lỗi.

### 6. Bài tập thực hành

1. **Bài tập 1**: Viết chương trình nhập vào một chuỗi và chuyển nó thành số nguyên. Xử lý ngoại lệ khi chuỗi không phải số hợp lệ.
2. **Bài tập 2**: Viết chương trình truy cập phần tử trong danh sách `List<int>`. Xử lý ngoại lệ khi truy cập chỉ số không hợp lệ.
3. **Bài tập 3**: Tạo một lớp `InvalidAgeException` và ném ngoại lệ này khi người dùng nhập tuổi lớn hơn 150.

#### Giải bài tập 1

```csharp
Console.WriteLine("Nhap mot chuoi so: ");
string input = Console.ReadLine();

try
{
    int number = int.Parse(input);
    Console.WriteLine($"So vua nhap: {number}");
}
catch (FormatException ex)
{
    Console.WriteLine("Loi: Chuoi khong phai so hop le!");
    Console.WriteLine($"Chi tiet loi: {ex.Message}");
}
```

#### Giải bài tập 2

```csharp
List<int> list = new List<int> { 10, 20, 30, 40 };
Console.WriteLine("Nhap chi so: ");
string input = Console.ReadLine();

try
{
    int index = int.Parse(input);
    Console.WriteLine($"Gia tri tai chi so {index}: {list[index]}");
}
catch (IndexOutOfRangeException ex)
{
    Console.WriteLine("Loi: Chi so vuot qua pham vi danh sach!");
    Console.WriteLine($"Chi tiet loi: {ex.Message}");
}
catch (FormatException ex)
{
    Console.WriteLine("Loi: Vui long nhap so hop le!");
    Console.WriteLine($"Chi tiet loi: {ex.Message}");
}
```

#### Giải bài tập 3

```csharp
class InvalidAgeException : Exception
{
    public InvalidAgeException(string message) : base(message) { }
}

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Nhap tuoi: ");
        string input = Console.ReadLine();

        try
        {
            int age = int.Parse(input);
            if (age > 150)
            {
                throw new InvalidAgeException("Tuoi khong the lon hon 150!");
            }
            Console.WriteLine($"Tuoi cua ban: {age}");
        }
        catch (InvalidAgeException ex)
        {
            Console.WriteLine($"Loi: {ex.Message}");
        }
        catch (FormatException ex)
        {
            Console.WriteLine("Loi: Vui long nhap so hop le!");
            Console.WriteLine($"Chi tiet loi: {ex.Message}");
        }
    }
}
```

## Kết luận

- Exception là công cụ quan trọng để xử lý lỗi trong lập trình .NET.
- Sử dụng `try-catch-finally` đúng cách giúp chương trình ổn định và dễ bảo trì.
- Tạo và sử dụng Exception tùy chỉnh khi cần xử lý các trường hợp lỗi đặc biệt.
- Áp dụng các best practices để viết code an toàn và dễ bảo trì.
