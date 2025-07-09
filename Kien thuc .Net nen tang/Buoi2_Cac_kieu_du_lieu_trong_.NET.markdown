# Học lập trình .NET - Buổi 2: Các kiểu dữ liệu trong .NET

## 1. Tổng quan về kiểu dữ liệu trong .NET

- **Kiểu dữ liệu** là cách xác định loại giá trị mà một biến có thể lưu trữ, ví dụ: số nguyên, số thực, chuỗi ký tự, v.v.
- Trong .NET, các kiểu dữ liệu được chia thành hai nhóm chính:
  - **Kiểu giá trị (Value Types)**: Lưu trữ trực tiếp giá trị trong bộ nhớ stack.
  - **Kiểu tham chiếu (Reference Types)**: Lưu trữ tham chiếu đến vị trí dữ liệu trong bộ nhớ heap.
- Các kiểu dữ liệu được định nghĩa trong **Common Type System (CTS)** của .NET.

### 1.1. Kiểu giá trị (Value Types)

- Bao gồm:
  - **Số nguyên**: `int`, `short`, `long`, `byte`, `sbyte`.
  - **Số thực**: `float`, `double`, `decimal`.
  - **Ký tự**: `char`.
  - **Logic**: `bool`.
  - **Cấu trúc (struct)**: Ví dụ `DateTime`, `TimeSpan`.
  - **Enum**: Kiểu liệt kê (user-defined).
- Đặc điểm:
  - Giá trị được lưu trực tiếp trong biến.
  - Khi gán, giá trị được sao chép (copy).
  - Mặc định: `0` (số), `false` (bool), `\0` (char).

### 1.2. Kiểu tham chiếu (Reference Types)

- Bao gồm:
  - **Chuỗi**: `string`.
  - **Mảng**: `int[]`, `string[]`, v.v.
  - **Lớp**: `class` (user-defined).
  - **Giao diện**: `interface`.
  - **Đối tượng**: `object`.
  - **Delegate**: Hàm tham chiếu.
- Đặc điểm:
  - Lưu trữ tham chiếu (địa chỉ) đến dữ liệu thực tế.
  - Khi gán, chỉ sao chép tham chiếu, không sao chép dữ liệu.
  - Mặc định: `null`.

### 1.3. Common Type System (CTS)

- CTS là hệ thống kiểu chung trong .NET, đảm bảo tính tương thích giữa các ngôn ngữ lập trình (C#, VB.NET, v.v.).
- Các kiểu dữ liệu trong CTS:
  - **Kiểu cơ bản**: `int`, `double`, `string`, v.v.
  - **Kiểu phức tạp**: Mảng, lớp, cấu trúc, v.v.

## 2. Các kiểu dữ liệu cơ bản trong .NET

Dưới đây là danh sách các kiểu dữ liệu cơ bản được đề cập trong video, kèm theo kích thước và phạm vi giá trị:

| Kiểu dữ liệu | Kích thước (byte) | Phạm vi giá trị      | Mô tả                                         |
| ------------ | ----------------- | -------------------- | --------------------------------------------- |
| `byte`       | 1                 | 0 → 255              | Số nguyên không âm, 8-bit                     |
| `sbyte`      | 1                 | \-128 → 127          | Số nguyên có dấu, 8-bit                       |
| `short`      | 2                 | \-32,768 → 32,767    | Số nguyên có dấu, 16-bit                      |
| `ushort`     | 2                 | 0 → 65,535           | Số nguyên không âm, 16-bit                    |
| `int`        | 4                 | \-2^31 → 2^31-1      | Số nguyên có dấu, 32-bit                      |
| `uint`       | 4                 | 0 → 2^32-1           | Số nguyên không âm, 32-bit                    |
| `long`       | 8                 | \-2^63 → 2^63-1      | Số nguyên có dấu, 64-bit                      |
| `ulong`      | 8                 | 0 → 2^64-1           | Số nguyên không âm, 64-bit                    |
| `float`      | 4                 | ±1.5E-45 → ±3.4E38   | Số thực, độ chính xác đơn                     |
| `double`     | 8                 | ±5.0E-324 → ±1.7E308 | Số thực, độ chính xác kép                     |
| `decimal`    | 16                | ±1.0E-28 → ±7.9E28   | Số thực, độ chính xác cao, dùng cho tài chính |
| `char`       | 2                 | U+0000 → U+FFFF      | Ký tự Unicode                                 |
| `bool`       | 1                 | `true` hoặc `false`  | Giá trị logic                                 |
| `string`     | Tham chiếu        | \-                   | Chuỗi ký tự Unicode                           |
| `object`     | Tham chiếu        | \-                   | Kiểu cơ sở của mọi kiểu dữ liệu               |

### 2.1. Ví dụ về khai báo biến

```csharp
int age = 25;              // Số nguyên
double salary = 5000.75;   // Số thực
char grade = 'A';          // Ký tự
bool isActive = true;      // Logic
string name = "Nguyen Van A"; // Chuỗi
```

## 3. Ép kiểu (Type Casting)

- **Ép kiểu ngầm (Implicit Casting)**: Chuyển từ kiểu nhỏ hơn sang kiểu lớn hơn, không mất dữ liệu.

  - Ví dụ: `int` → `double`.

  ```csharp
  int num = 100;
  double doubleNum = num; // Ép kiểu ngầm
  Console.WriteLine(doubleNum); // Kết quả: 100.0
  ```

- **Ép kiểu tường minh (Explicit Casting)**: Chuyển từ kiểu lớn hơn sang kiểu nhỏ hơn, có thể mất dữ liệu.

  - Sử dụng toán tử `(type)` hoặc các phương thức như `Convert.ToInt32()`, `int.Parse()`.

  ```csharp
  double num = 123.45;
  int intNum = (int)num; // Ép kiểu tường minh
  Console.WriteLine(intNum); // Kết quả: 123
  ```

- **Sử dụng Convert class**:

  ```csharp
  string strNum = "123";
  int number = Convert.ToInt32(strNum);
  Console.WriteLine(number); // Kết quả: 123
  ```

- **Sử dụng Parse**:

  ```csharp
  string strNum = "456";
  int number = int.Parse(strNum);
  Console.WriteLine(number); // Kết quả: 456
  ```

## 4. Nullable Types

- **Nullable Types** cho phép các kiểu giá trị (như `int`, `double`) có thể nhận giá trị `null`.
- Cú pháp: `Type?` (ví dụ: `int?`, `double?`).
- Sử dụng thuộc tính:
  - `HasValue`: Kiểm tra biến có giá trị hay không.
  - `Value`: Lấy giá trị nếu `HasValue` là `true`.
- Ví dụ:

  ```csharp
  int? nullableInt = null;
  if (nullableInt.HasValue)
  {
      Console.WriteLine(nullableInt.Value);
  }
  else
  {
      Console.WriteLine("Giá trị là null");
  }
  // Kết quả: Giá trị là null
  ```

## 5. Boxing và Unboxing

- **Boxing**: Chuyển từ kiểu giá trị (`int`, `double`, v.v.) sang kiểu tham chiếu (`object`).

  ```csharp
  int num = 123;
  object obj = num; // Boxing
  ```

- **Unboxing**: Chuyển từ kiểu tham chiếu (`object`) về kiểu giá trị.

  ```csharp
  object obj = 123;
  int num = (int)obj; // Unboxing
  ```

- **Lưu ý**: Boxing và unboxing có thể ảnh hưởng đến hiệu suất, nên hạn chế sử dụng khi không cần thiết.

## 6. Bài tập ví dụ

### Bài tập 1: Khai báo và hiển thị các kiểu dữ liệu

Viết chương trình khai báo các biến với các kiểu dữ liệu khác nhau và hiển thị giá trị của chúng.

```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        int age = 30;
        double salary = 75000.50;
        char grade = 'B';
        bool isEmployed = true;
        string name = "Tran Thi B";

        Console.WriteLine("Tên: " + name);
        Console.WriteLine("Tuổi: " + age);
        Console.WriteLine("Lương: " + salary);
        Console.WriteLine("Xếp loại: " + grade);
        Console.WriteLine("Tình trạng việc làm: " + isEmployed);
    }
}
```

**Kết quả**:

```
Tên: Tran Thi B
Tuổi: 30
Lương: 75000.5
Xếp loại: B
Tình trạng việc làm: True
```

### Bài tập 2: Ép kiểu và tính toán

Viết chương trình nhập vào một chuỗi số, chuyển thành kiểu `int` và tính bình phương của số đó.

```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Nhập một số: ");
        string input = Console.ReadLine();
        int number = int.Parse(input);
        int square = number * number;
        Console.WriteLine("Bình phương của " + number + " là: " + square);
    }
}
```

**Kết quả** (ví dụ nhập `5`):

```
Nhập một số:
5
Bình phương của 5 là: 25
```

### Bài tập 3: Sử dụng Nullable Types

Viết chương trình kiểm tra một biến `int?` có giá trị hay không, và hiển thị giá trị nếu có.

```csharp
using System;

class Program
{
    static void Main(string[] args)
    {
        int? nullableNumber = 100;
        if (nullableNumber.HasValue)
        {
            Console.WriteLine("Giá trị: " + nullableNumber.Value);
        }
        else
        {
            Console.WriteLine("Giá trị là null");
        }

        nullableNumber = null;
        if (nullableNumber.HasValue)
        {
            Console.WriteLine("Giá trị: " + nullableNumber.Value);
        }
        else
        {
            Console.WriteLine("Giá trị là null");
        }
    }
}
```

**Kết quả**:

```
Giá trị: 100
Giá trị là null
```

## 7. Một số lưu ý

- **Kiểm tra lỗi khi ép kiểu**:

  - Sử dụng `try-catch` khi dùng `Parse` hoặc `Convert` để xử lý lỗi nhập liệu.

  ```csharp
  try
  {
      string input = "abc";
      int number = int.Parse(input);
  }
  catch (FormatException)
  {
      Console.WriteLine("Lỗi: Chuỗi không phải số hợp lệ");
  }
  ```

- **Hiệu suất**:

  - Tránh lạm dụng boxing/unboxing vì ảnh hưởng đến hiệu suất.
  - Sử dụng kiểu dữ liệu phù hợp với nhu cầu (ví dụ: dùng `decimal` cho tính toán tài chính).

- **Kiểu** `string`:

  - Là kiểu tham chiếu, nhưng hoạt động giống kiểu giá trị (immutable - không thể thay đổi nội dung sau khi tạo).

## 8. Kết luận

- Hiểu rõ các kiểu dữ liệu trong .NET là nền tảng quan trọng để lập trình hiệu quả.
- Cần nắm vững sự khác biệt giữa kiểu giá trị và kiểu tham chiếu, cách ép kiểu, và sử dụng nullable types để xử lý các trường hợp đặc biệt.
- Thực hành qua các bài tập giúp củng cố kiến thức và áp dụng vào các dự án thực tế.
