# Học lập trình .NET - Phần 5 - Phương thức

Tài liệu này tổng hợp kiến thức, ví dụ và bài tập từ video YouTube "[Học lập trình .NET - Phần 5 - Phương thức](https://www.youtube.com/watch?v=Lg7n1nyrQTc&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=5)" thuộc series học lập trình .NET cơ bản. Nội dung tập trung vào khái niệm **phương thức** trong C#, bao gồm định nghĩa, cú pháp, các loại phương thức, ví dụ minh họa và bài tập thực hành.

---

## 1. Khái niệm phương thức

### Phương thức là gì?

- Phương thức (method) là một khối mã thực hiện một nhiệm vụ cụ thể.
- Được đặt tên và có thể gọi nhiều lần trong chương trình.
- Vai trò:
  - Tái sử dụng mã (code reusability).
  - Tăng tính module hóa, dễ bảo trì.
  - Giảm lặp lại mã (DRY - Don't Repeat Yourself).

### Cú pháp cơ bản

```csharp
[access_modifier] [return_type] MethodName([parameters])
{
    // Thân phương thức
}
```

- **access_modifier**: Quyền truy cập (public, private, protected, v.v.). Mặc định là private nếu không khai báo.
- **return_type**: Kiểu dữ liệu trả về (int, string, void, v.v.). Nếu không trả về, dùng `void`.
- **MethodName**: Tên phương thức, theo chuẩn PascalCase.
- **parameters**: Tham số đầu vào (tùy chọn).

**Ví dụ**:

```csharp
public void SayHello()
{
    Console.WriteLine("Hello, World!");
}
```

### Gọi phương thức

- Gọi tên phương thức kèm dấu ngoặc `()`.
- Nếu có tham số, truyền tham số tương ứng.

**Ví dụ**:

```csharp
SayHello(); // In: Hello, World!
```

---

## 2. Các loại phương thức

Dựa trên việc có tham số và giá trị trả về, phương thức được chia thành 4 loại:

### 2.1. Không tham số, không giá trị trả về

**Cú pháp**:

```csharp
public void MethodName()
{
    // Code
}
```

**Ví dụ**:

```csharp
public void SayHello()
{
    Console.WriteLine("Xin chào, đây là phương thức đầu tiên!");
}
```

**Gọi**:

```csharp
SayHello(); // In: Xin chào, đây là phương thức đầu tiên!
```

### 2.2. Có tham số, không giá trị trả về

**Cú pháp**:

```csharp
public void MethodName(parameter_type parameter_name)
{
    // Code
}
```

**Ví dụ**:

```csharp
public void SayHelloTo(string name)
{
    Console.WriteLine($"Xin chào, {name}!");
}
```

**Gọi**:

```csharp
SayHelloTo("John"); // In: Xin chào, John!
```

### 2.3. Không tham số, có giá trị trả về

**Cú pháp**:

```csharp
public return_type MethodName()
{
    // Code
    return value;
}
```

**Ví dụ**:

```csharp
public int GetRandomNumber()
{
    Random rand = new Random();
    return rand.Next(1, 100);
}
```

**Gọi**:

```csharp
int number = GetRandomNumber();
Console.WriteLine(number); // In số ngẫu nhiên từ 1 đến 99
```

### 2.4. Có tham số, có giá trị trả về

**Cú pháp**:

```csharp
public return_type MethodName(parameter_type parameter_name)
{
    // Code
    return value;
}
```

**Ví dụ**:

```csharp
public int Square(int number)
{
    return number * number;
}
```

**Gọi**:

```csharp
int result = Square(5);
Console.WriteLine(result); // In: 25
```

---

## 3. Ví dụ minh họa

### Ví dụ 1: In thông tin cá nhân

**Yêu cầu**: Tạo phương thức nhận tên và tuổi, in thông tin.

**Code**:

```csharp
public void PrintPersonalInfo(string name, int age)
{
    Console.WriteLine($"Tên: {name}, Tuổi: {age}");
}
```

**Gọi**:

```csharp
PrintPersonalInfo("Alice", 25); // In: Tên: Alice, Tuổi: 25
```

### Ví dụ 2: Tính tổng hai số

**Yêu cầu**: Tạo phương thức nhận hai số nguyên, trả về tổng.

**Code**:

```csharp
public int Sum(int a, int b)
{
    return a + b;
}
```

**Gọi**:

```csharp
int result = Sum(3, 4);
Console.WriteLine(result); // In: 7
```

### Ví dụ 3: Kiểm tra số chẵn

**Yêu cầu**: Tạo phương thức nhận số nguyên, trả về true nếu chẵn, false nếu lẻ.

**Code**:

```csharp
public bool IsEven(int number)
{
    return number % 2 == 0;
}
```

**Gọi**:

```csharp
bool isEven = IsEven(6);
Console.WriteLine(isEven); // In: True
```

---

## 4. Bài tập thực hành

### Bài tập 1: Tính diện tích hình chữ nhật

**Yêu cầu**: Viết phương thức nhận chiều dài và chiều rộng, trả về diện tích.

**Lời giải**:

```csharp
public double CalculateRectangleArea(double length, double width)
{
    return length * width;
}
```

**Thực thi**:

```csharp
double area = CalculateRectangleArea(5.5, 3.2);
Console.WriteLine($"Diện tích: {area}"); // In: Diện tích: 17.6
```

### Bài tập 2: Chuyển đổi nhiệt độ

**Yêu cầu**: Viết phương thức nhận nhiệt độ Celsius, trả về Fahrenheit (F = C \* 9/5 + 32).

**Lời giải**:

```csharp
public double CelsiusToFahrenheit(double celsius)
{
    return celsius * 9 / 5 + 32;
}
```

**Thực thi**:

```csharp
double fahrenheit = CelsiusToFahrenheit(25);
Console.WriteLine($"Nhiệt độ Fahrenheit: {fahrenheit}"); // In: Nhiệt độ Fahrenheit: 77
```

### Bài tập 3: Kiểm tra số nguyên tố

**Yêu cầu**: Viết phương thức nhận số nguyên, trả về true nếu là số nguyên tố, false nếu không.

**Lời giải**:

```csharp
public bool IsPrime(int number)
{
    if (number < 2) return false;
    for (int i = 2; i <= Math.Sqrt(number); i++)
    {
        if (number % i == 0) return false;
    }
    return true;
}
```

**Thực thi**:

```csharp
bool isPrime = IsPrime(17);
Console.WriteLine(isPrime); // In: True
```

---

## 5. Lưu ý quan trọng

- **Đặt tên phương thức**: Dùng động từ hoặc cụm động từ mô tả chức năng (VD: `CalculateArea`, `PrintInfo`).
- **Tham số**: Đặt tên tham số dễ hiểu, tránh tên chung chung như `x`, `y`.
- **Giá trị trả về**: Đảm bảo tất cả nhánh code trong phương thức có giá trị trả về nếu không phải `void`.
- **Quyền truy cập**: Khai báo `public` để gọi từ ngoài lớp, nếu không cần thì dùng `private`.

---

## 6. Tổng hợp kiến thức

| **Loại phương thức**                | **Ví dụ cú pháp**                             | **Ứng dụng**                         |
| ----------------------------------- | --------------------------------------------- | ------------------------------------ |
| Không tham số, không giá trị trả về | `public void SayHello()`                      | Thực hiện hành động đơn giản.        |
| Có tham số, không giá trị trả về    | `public void PrintInfo(string name, int age)` | Xử lý dữ liệu đầu vào.               |
| Không tham số, có giá trị trả về    | `public int GetRandomNumber()`                | Trả về dữ liệu mà không cần đầu vào. |
| Có tham số, có giá trị trả về       | `public int Sum(int a, int b)`                | Xử lý dữ liệu và trả về kết quả.     |

---

## 7. Lời khuyên học tập

- **Thực hành**: Viết lại các ví dụ, sáng tạo phương thức mới.
- **Debug**: Sử dụng breakpoint trong Visual Studio để hiểu cách hoạt động.
- **Tài liệu**: Đọc tài liệu C# của Microsoft.
- **Bài tập**: Luyện tập trên LeetCode, HackerRank để rèn kỹ năng.

---

## 8. Kết luận

Phương thức là nền tảng quan trọng trong lập trình C# và .NET. Video này cung cấp kiến thức cơ bản, ví dụ rõ ràng và bài tập thực hành hữu ích. Nắm vững phương thức sẽ giúp bạn dễ dàng tiếp cận các khái niệm nâng cao như lớp, đối tượng và lập trình hướng đối tượng.
