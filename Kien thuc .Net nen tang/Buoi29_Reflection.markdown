# Buổi 29: Reflection trong .NET

## 1. Tổng quan về Reflection

### 1.1. Reflection là gì?

- **Reflection** là cơ chế trong .NET cho phép chương trình kiểm tra và thao tác với các thành phần (metadata) của chính nó hoặc của các assembly khác trong thời gian chạy (runtime).
- Reflection giúp truy cập thông tin về các class, method, property, field, attribute, v.v. mà không cần biết trước chi tiết tại thời điểm biên dịch (compile time).
- Ứng dụng của Reflection:
  - Kiểm tra cấu trúc của một đối tượng (class, interface, enum, v.v.).
  - Gọi method, truy cập property/field động (dynamic).
  - Tạo đối tượng động hoặc tải assembly trong runtime.
  - Sử dụng trong các framework như ASP.NET, Entity Framework để mapping hoặc xử lý attribute.

### 1.2. Namespace chính

- Reflection nằm trong namespace: `System.Reflection`.
- Một số class quan trọng:
  - `Type`: Đại diện cho kiểu dữ liệu (class, interface, struct, v.v.).
  - `Assembly`: Đại diện cho một assembly (file .dll hoặc .exe).
  - `MethodInfo`, `PropertyInfo`, `FieldInfo`: Đại diện cho các thành phần của class (method, property, field).
  - `ConstructorInfo`: Đại diện cho constructor.

## 2. Các khái niệm và ví dụ cơ bản

### 2.1. Lấy thông tin về Type

- Sử dụng class `Type` để lấy thông tin về một kiểu dữ liệu.
- Các cách lấy đối tượng `Type`:
  - `typeof(T)`: Lấy Type của một kiểu cụ thể (ví dụ: `typeof(string)`).
  - `object.GetType()`: Lấy Type của một đối tượng tại runtime.
  - `Type.GetType("Namespace.TypeName")`: Lấy Type từ tên đầy đủ.

**Ví dụ 1: Lấy thông tin về Type của một class**

```csharp
using System;
using System.Reflection;

class Program
{
    static void Main(string[] args)
    {
        // Lấy Type của lớp string
        Type type = typeof(string);

        Console.WriteLine($"Tên lớp: {type.Name}");
        Console.WriteLine($"Namespace: {type.Namespace}");
        Console.WriteLine($"Là class: {type.IsClass}");
        Console.WriteLine($"Là abstract: {type.IsAbstract}");
    }
}
```

**Kết quả**:

```
Tên lớp: String
Namespace: System
Là class: True
Là abstract: False
```

### 2.2. Lấy thông tin về các thành viên của Type

- Sử dụng các phương thức như:
  - `GetMethods()`: Lấy danh sách các method.
  - `GetProperties()`: Lấy danh sách các property.
  - `GetFields()`: Lấy danh sách các field.
  - `GetConstructors()`: Lấy danh sách các constructor.

**Ví dụ 2: Lấy danh sách method của một class**

```csharp
using System;
using System.Reflection;

class Program
{
    static void Main(string[] args)
    {
        Type type = typeof(string);
        MethodInfo[] methods = type.GetMethods();

        Console.WriteLine("Danh sách các phương thức của string:");
        foreach (var method in methods)
        {
            Console.WriteLine(method.Name);
        }
    }
}
```

**Kết quả**: In Handicap danh sách các method của class `string` (như `ToString`, `Substring`, `IndexOf`, v.v.).

### 2.3. Tạo và gọi đối tượng động

- Reflection cho phép tạo đối tượng và gọi method mà không cần biết trước kiểu dữ liệu.

**Ví dụ 3: Tạo đối tượng và gọi method động**

```csharp
using System;
using System.Reflection;

class Person
{
    public string Name { get; set; }
    public void SayHello()
    {
        Console.WriteLine($"Hello, I'm {Name}");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Type type = typeof(Person);

        // Tạo đối tượng Person động
        object person = Activator.CreateInstance(type);

        // Set giá trị cho property Name
        PropertyInfo nameProperty = type.GetProperty("Name");
        nameProperty.SetValue(person, "John");

        // Gọi method SayHello
        MethodInfo sayHelloMethod = type.GetMethod("SayHello");
        sayHelloMethod.Invoke(person, null);
    }
}
```

**Kết quả**:

```
Hello, I'm John
```

### 2.4. Làm việc với Attribute

- Reflection thường được sử dụng để đọc các attribute tùy chỉnh (custom attribute).

**Ví dụ 4: Đọc custom attribute**

```csharp
using System;
using System.Reflection;

[AttributeUsage(AttributeTargets.Method)]
class MyCustomAttribute : Attribute
{
    public string Description { get; }

    public MyCustomAttribute(string description)
    {
        Description = description;
    }
}

class Person
{
    [MyCustomAttribute("Phương thức chào hỏi")]
    public void SayHello()
    {
        Console.WriteLine("Hello, World!");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Type type = typeof(Person);
        MethodInfo method = type.GetMethod("SayHello");

        MyCustomAttribute attribute = (MyCustomAttribute)Attribute.GetCustomAttribute(method, typeof(MyCustomAttribute));

        Console.WriteLine($"Mô tả: {attribute.Description}");
    }
}
```

**Kết quả**:

```
Mô tả: Phương thức chào hỏi
```

## 3. Lưu ý khi sử dụng Reflection

- **Hiệu suất**: Reflection chậm hơn so với truy cập trực tiếp vì nó phải truy vấn metadata trong runtime.
- **Bảo mật**: Cần cẩn thận khi sử dụng Reflection để truy cập các thành phần private, vì có thể vi phạm các quy tắc bảo mật.
- **Ứng dụng thực tế**:
  - Sử dụng trong các framework để tự động mapping (ví dụ: Entity Framework).
  - Kiểm tra và xác thực code động.
  - Tạo các công cụ kiểm thử (unit testing framework).

## 4. Bài tập thực hành

### Bài tập 1: Liệt kê tất cả các property của một class

- **Yêu cầu**: Viết chương trình liệt kê tất cả các property của class `System.DateTime`.
- **Gợi ý**: Sử dụng `Type.GetProperties()`.

**Đáp án**:

```csharp
using System;
using System.Reflection;

class Program
{
    static void Main(string[] args)
    {
        Type type = typeof(DateTime);
        PropertyInfo[] properties = type.GetProperties();

        Console.WriteLine("Danh sách các property của DateTime:");
        foreach (var property in properties)
        {
            Console.WriteLine(property.Name);
        }
    }
}
```

### Bài tập 2: Gọi method động

- **Yêu cầu**: Viết chương trình tạo một đối tượng `List<string>` động, thêm một chuỗi vào danh sách và gọi method `Count` để lấy số lượng phần tử.
- **Gợi ý**: Sử dụng `Activator.CreateInstance` và `MethodInfo.Invoke`.

**Đáp án**:

```csharp
using System;
using System.Collections.Generic;
using System.Reflection;

class Program
{
    static void Main(string[] args)
    {
        Type type = typeof(List<string>);

        // Tạo đối tượng List<string>
        object list = Activator.CreateInstance(type);

        // Gọi method Add
        MethodInfo addMethod = type.GetMethod("Add");
        addMethod.Invoke(list, new object[] { "Hello" });

        // Gọi method Count
        PropertyInfo countProperty = type.GetProperty("Count");
        int count = (int)countProperty.GetValue(list);

        Console.WriteLine($"Số lượng phần tử: {count}");
    }
}
```

**Kết quả**:

```
Số lượng phần tử: 1
```

## 5. Tài liệu tham khảo

- Tài liệu chính thức của Microsoft về Reflection: [https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/reflection](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/reflection)
- Video YouTube: [Học lập trình .NET - Phần 29 - Reflection](https://www.youtube.com/watch?v=JQx8E1Y_Bac&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=29)
