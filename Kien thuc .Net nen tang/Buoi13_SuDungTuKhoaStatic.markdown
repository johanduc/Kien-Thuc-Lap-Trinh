# Buổi 13: Sử dụng từ khóa Static

## Mục tiêu

- Hiểu khái niệm và cách sử dụng từ khóa `static` trong lập trình .NET.
- Phân biệt các thành phần `static` và non-static.
- Ứng dụng `static` trong các tình huống thực tế.

## Nội dung chính

### 1. Từ khóa `static` là gì?

- `static` là một từ khóa trong C# dùng để khai báo các thành phần (biến, phương thức, lớp, v.v.) thuộc về **lớp** thay vì thuộc về **đối tượng** (instance) của lớp.
- Các thành phần `static`:
  - Được chia sẻ chung cho tất cả các đối tượng của lớp.
  - Có thể truy cập mà không cần tạo đối tượng của lớp.
  - Chỉ được khởi tạo một lần duy nhất khi lớp được nạp vào bộ nhớ.

### 2. Các thành phần có thể sử dụng `static`

- **Biến static** (static field): Lưu trữ dữ liệu chung cho tất cả các đối tượng.
- **Phương thức static** (static method): Có thể gọi mà không cần tạo đối tượng.
- **Lớp static** (static class): Chỉ chứa các thành phần static, không thể khởi tạo đối tượng.
- **Hàm tạo static** (static constructor): Dùng để khởi tạo các thành phần static của lớp.

### 3. Đặc điểm của `static`

- **Chỉ có một bản duy nhất**: Các thành phần static được lưu trữ ở một vùng bộ nhớ duy nhất, không phụ thuộc vào số lượng đối tượng.
- **Truy cập trực tiếp qua tên lớp**: Ví dụ, `ClassName.StaticMethod()` thay vì `new ClassName().Method()`.
- **Không thể truy cập các thành phần non-static**: Trong phương thức static, không thể truy cập trực tiếp các biến hoặc phương thức non-static vì chúng thuộc về đối tượng cụ thể.
- **Hàm tạo static**:
  - Chỉ chạy một lần khi lớp được nạp.
  - Không có tham số.
  - Không có từ khóa truy cập (public, private, v.v.).

### 4. Ví dụ minh họa

#### Ví dụ 1: Biến static

```csharp
public class Student
{
    public static int Count = 0; // Biến static đếm số lượng sinh viên
    public string Name;

    public Student(string name)
    {
        Name = name;
        Count++; // Tăng số lượng sinh viên khi tạo mới
    }
}

class Program
{
    static void Main(string[] args)
    {
        Student s1 = new Student("Nguyen Van A");
        Console.WriteLine($"Số sinh viên: {Student.Count}"); // Output: 1

        Student s2 = new Student("Tran Thi B");
        Console.WriteLine($"Số sinh viên: {Student.Count}"); // Output: 2
    }
}
```

**Giải thích**:

- Biến `Count` là static, được chia sẻ cho tất cả các đối tượng của lớp `Student`.
- Mỗi lần tạo một đối tượng `Student`, `Count` tăng lên 1.

#### Ví dụ 2: Phương thức static

```csharp
public class Calculator
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
}

class Program
{
    static void Main(string[] args)
    {
        int result = Calculator.Add(5, 3); // Gọi trực tiếp qua tên lớp
        Console.WriteLine($"Tổng: {result}"); // Output: 8
    }
}
```

**Giải thích**:

- Phương thức `Add` là static, có thể gọi trực tiếp mà không cần tạo đối tượng của lớp `Calculator`.

#### Ví dụ 3: Lớp static

```csharp
public static class MathUtils
{
    public static double PI = 3.14159;

    public static double CalculateCircleArea(double radius)
    {
        return PI * radius * radius;
    }
}

class Program
{
    static void Main(string[] args)
    {
        double area = MathUtils.CalculateCircleArea(5);
        Console.WriteLine($"Diện tích hình tròn: {area}"); // Output: 78.53975
    }
}
```

**Giải thích**:

- Lớp `MathUtils` là static, chỉ chứa các thành phần static.
- Không thể tạo đối tượng của lớp static (`new MathUtils()` sẽ báo lỗi).

#### Ví dụ 4: Hàm tạo static

```csharp
public class DatabaseConnection
{
    public static string ConnectionString;

    // Hàm tạo static
    static DatabaseConnection()
    {
        ConnectionString = "Server=localhost;Database=TestDB;Trusted_Connection=True;";
        Console.WriteLine("Hàm tạo static được gọi");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine(DatabaseConnection.ConnectionString); // Hàm tạo static chạy lần đầu
        Console.WriteLine(DatabaseConnection.ConnectionString); // Không chạy lại
    }
}
```

**Giải thích**:

- Hàm tạo static chỉ chạy một lần khi lớp `DatabaseConnection` được sử dụng lần đầu.
- `ConnectionString` được khởi tạo giá trị trong hàm tạo static.

### 5. Ứng dụng thực tế của `static`

- **Quản lý tài nguyên chung**: Ví dụ, lưu trữ chuỗi kết nối cơ sở dữ liệu, biến đếm số lượng đối tượng.
- **Tiện ích (Utility)**: Các phương thức tiện ích như tính toán, chuyển đổi định dạng (ví dụ: lớp `Math` trong .NET).
- **Cấu hình toàn cục**: Lưu trữ các giá trị cấu hình không thay đổi trong suốt vòng đời ứng dụng.
- **Singleton pattern**: Đảm bảo chỉ có một instance của lớp (dùng kết hợp với static).

### 6. Lưu ý khi sử dụng `static`

- **Hạn chế lạm dụng**: Sử dụng quá nhiều static có thể làm mã khó bảo trì và kiểm thử.
- **Thread safety**: Các thành phần static được chia sẻ giữa các luồng, cần cẩn thận khi làm việc với đa luồng.
- **Không thể kế thừa**: Lớp static không thể được kế thừa hoặc sử dụng như một lớp cơ sở.

### 7. Bài tập thực hành

1. **Bài tập 1**: Viết một lớp `Counter` với biến static `count` để đếm số lần một phương thức được gọi. Tạo phương thức static `Increment` để tăng giá trị `count` và phương thức static `GetCount` để lấy giá trị `count`.

```csharp
public class Counter
{
    private static int count = 0;

    public static void Increment()
    {
        count++;
    }

    public static int GetCount()
    {
        return count;
    }
}

class Program
{
    static void Main(string[] args)
    {
        Counter.Increment();
        Counter.Increment();
        Console.WriteLine($"Số lần gọi: {Counter.GetCount()}"); // Output: 2
    }
}
```

2. **Bài tập 2**: Viết một lớp static `StringUtils` với phương thức static `ReverseString` để đảo ngược chuỗi ký tự.

```csharp
public static class StringUtils
{
    public static string ReverseString(string input)
    {
        char[] charArray = input.ToCharArray();
        Array.Reverse(charArray);
        return new string(charArray);
    }
}

class Program
{
    static void Main(string[] args)
    {
        string result = StringUtils.ReverseString("Hello, World!");
        Console.WriteLine(result); // Output: !dlroW ,olleH
    }
}
```

## Kết luận

- Từ khóa `static` rất hữu ích trong việc quản lý tài nguyên chung và tạo các phương thức tiện ích.
- Cần sử dụng `static` một cách hợp lý để tránh làm phức tạp hóa mã nguồn và đảm bảo tính dễ bảo trì.
