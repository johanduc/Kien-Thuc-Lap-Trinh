# Buổi 15: Giải bài tập, Unit Test, Abstract Factory và Decorator Design Pattern

## 1. Tổng quan nội dung buổi học

Trong buổi học này, chúng ta sẽ tìm hiểu các nội dung chính sau:

- **Giải bài tập thực hành**: Các bài tập thực tế liên quan đến lập trình .NET.
- **Unit Test**: Cách viết và chạy unit test trong .NET để đảm bảo chất lượng mã nguồn.
- **Abstract Factory Design Pattern**: Một mẫu thiết kế sáng tạo (Creational Pattern) để tạo ra các nhóm đối tượng liên quan mà không cần chỉ định lớp cụ thể.
- **Decorator Design Pattern**: Một mẫu thiết kế cấu trúc (Structural Pattern) để mở rộng chức năng của đối tượng một cách linh hoạt.

---

## 2. Giải bài tập thực hành

Các bài tập thực hành thường nhằm củng cố kiến thức về lập trình .NET, bao gồm cách sử dụng các cấu trúc điều khiển, lớp, giao diện, và các thư viện trong .NET.

### Bài tập ví dụ: Quản lý danh sách sinh viên

**Yêu cầu**:

- Tạo một lớp `Student` với các thuộc tính: `Id`, `Name`, `GPA` (điểm trung bình).
- Viết phương thức để:
  - Thêm sinh viên vào danh sách.
  - Tìm sinh viên theo tên (không phân biệt hoa thường).
  - Sắp xếp danh sách sinh viên theo GPA giảm dần.

**Giải pháp**:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public double GPA { get; set; }

    public Student(int id, string name, double gpa)
    {
        Id = id;
        Name = name;
        GPA = gpa;
    }
}

public class StudentManager
{
    private List<Student> students = new List<Student>();

    // Thêm sinh viên
    public void AddStudent(Student student)
    {
        students.Add(student);
    }

    // Tìm sinh viên theo tên
    public List<Student> FindStudentsByName(string name)
    {
        return students.Where(s => s.Name.ToLower().Contains(name.ToLower())).ToList();
    }

    // Sắp xếp theo GPA giảm dần
    public List<Student> SortByGPA()
    {
        return students.OrderByDescending(s => s.GPA).ToList();
    }
}

class Program
{
    static void Main(string[] args)
    {
        var manager = new StudentManager();
        manager.AddStudent(new Student(1, "Nguyen Van A", 8.5));
        manager.AddStudent(new Student(2, "Tran Thi B", 7.8));
        manager.AddStudent(new Student(3, "Le Van C", 9.0));

        // Tìm sinh viên
        var foundStudents = manager.FindStudentsByName("van");
        Console.WriteLine("Sinh viên tìm thấy:");
        foreach (var student in foundStudents)
        {
            Console.WriteLine($"ID: {student.Id}, Name: {student.Name}, GPA: {student.GPA}");
        }

        // Sắp xếp theo GPA
        var sortedStudents = manager.SortByGPA();
        Console.WriteLine("\nDanh sách sinh viên sắp xếp theo GPA:");
        foreach (var student in sortedStudents)
        {
            Console.WriteLine($"ID: {student.Id}, Name: {student.Name}, GPA: {student.GPA}");
        }
    }
}
```

**Kết quả**:

- Tìm kiếm sinh viên có chứa "van" trong tên: Nguyen Van A, Le Van C.
- Sắp xếp theo GPA giảm dần: Le Van C (9.0), Nguyen Van A (8.5), Tran Thi B (7.8).

---

## 3. Unit Test trong .NET

Unit Test là quá trình kiểm tra các đơn vị nhỏ nhất của mã nguồn (thường là các phương thức) để đảm bảo chúng hoạt động đúng như kỳ vọng. Trong .NET, các framework phổ biến để viết unit test bao gồm **MSTest**, **NUnit**, và **xUnit**.

### Ví dụ Unit Test cho lớp `StudentManager`

**Môi trường**: Sử dụng xUnit để viết unit test.

**Cài đặt**:

- Thêm package `xUnit` và `xUnit.runner.visualstudio` vào dự án.
- Tạo một dự án test riêng (ví dụ: `StudentManager.Tests`).

**Mã Unit Test**:

```csharp
using System.Collections.Generic;
using Xunit;

public class StudentManagerTests
{
    [Fact]
    public void AddStudent_ShouldIncreaseCount()
    {
        // Arrange
        var manager = new StudentManager();
        var student = new Student(1, "Nguyen Van A", 8.5);

        // Act
        manager.AddStudent(student);

        // Assert
        Assert.Single(manager.FindStudentsByName("Nguyen Van A"));
    }

    [Fact]
    public void FindStudentsByName_ShouldReturnCorrectStudents()
    {
        // Arrange
        var manager = new StudentManager();
        manager.AddStudent(new Student(1, "Nguyen Van A", 8.5));
        manager.AddStudent(new Student(2, "Tran Thi B", 7.8));

        // Act
        var foundStudents = manager.FindStudentsByName("van");

        // Assert
        Assert.Single(foundStudents);
        Assert.Equal("Nguyen Van A", foundStudents[0].Name);
    }

    [Fact]
    public void SortByGPA_ShouldSortDescending()
    {
        // Arrange
        var manager = new StudentManager();
        manager.AddStudent(new Student(1, "Nguyen Van A", 8.5));
        manager.AddStudent(new Student(2, "Tran Thi B", 7.8));
        manager.AddStudent(new Student(3, "Le Van C", 9.0));

        // Act
        var sortedStudents = manager.SortByGPA();

        // Assert
        Assert.Equal(9.0, sortedStudents[0].GPA);
        Assert.Equal(8.5, sortedStudents[1].GPA);
        Assert.Equal(7.8, sortedStudents[2].GPA);
    }
}
```

**Giải thích**:

- `[Fact]`: Đánh dấu một phương thức là một test case.
- **Arrange**: Chuẩn bị dữ liệu và đối tượng cần thiết.
- **Act**: Thực thi phương thức cần kiểm tra.
- **Assert**: Kiểm tra kết quả có đúng như kỳ vọng không.

**Chạy test**:

- Sử dụng Visual Studio Test Explorer hoặc lệnh `dotnet test` để chạy các unit test.
- Kết quả sẽ báo pass/fail cho từng test case.

---

## 4. Abstract Factory Design Pattern

**Abstract Factory** là một mẫu thiết kế sáng tạo, cung cấp một giao diện để tạo ra các nhóm đối tượng liên quan mà không cần chỉ định lớp cụ thể của chúng.

### Ứng dụng

- Sử dụng khi cần tạo ra các đối tượng thuộc nhiều loại khác nhau nhưng có liên quan (ví dụ: giao diện người dùng cho Windows và macOS).
- Đảm bảo tính nhất quán giữa các đối tượng được tạo ra.

### Ví dụ: Tạo giao diện người dùng cho Windows và macOS

**Cấu trúc**:

- **Abstract Factory**: Định nghĩa giao diện cho việc tạo các đối tượng (Button, TextBox).
- **Concrete Factory**: Cung cấp triển khai cụ thể cho Windows hoặc macOS.
- **Abstract Product**: Giao diện cho các sản phẩm (Button, TextBox).
- **Concrete Product**: Các lớp cụ thể (WindowsButton, MacButton).

**Mã ví dụ**:

```csharp
using System;

// Abstract Products
public interface IButton
{
    void Render();
}

public interface ITextBox
{
    void Render();
}

// Concrete Products
public class WindowsButton : IButton
{
    public void Render() => Console.WriteLine("Render Windows Button");
}

public class WindowsTextBox : ITextBox
{
    public void Render() => Console.WriteLine("Render Windows TextBox");
}

public class MacButton : IButton
{
    public void Render() => Console.WriteLine("Render Mac Button");
}

public class MacTextBox : ITextBox
{
    public void Render() => Console.WriteLine("Render Mac TextBox");
}

// Abstract Factory
public interface IUIFactory
{
    IButton CreateButton();
    ITextBox CreateTextBox();
}

// Concrete Factories
public class WindowsUIFactory : IUIFactory
{
    public IButton CreateButton() => new WindowsButton();
    public ITextBox CreateTextBox() => new WindowsTextBox();
}

public class MacUIFactory : IUIFactory
{
    public IButton CreateButton() => new MacButton();
    public ITextBox CreateTextBox() => new MacTextBox();
}

// Client
public class Application
{
    private readonly IButton _button;
    private readonly ITextBox _textBox;

    public Application(IUIFactory factory)
    {
        _button = factory.CreateButton();
        _textBox = factory.CreateTextBox();
    }

    public void RenderUI()
    {
        _button.Render();
        _textBox.Render();
    }
}

class Program
{
    static void Main(string[] args)
    {
        IUIFactory factory;

        // Tạo UI cho Windows
        factory = new WindowsUIFactory();
        var windowsApp = new Application(factory);
        windowsApp.RenderUI();

        // Tạo UI cho macOS
        factory = new MacUIFactory();
        var macApp = new Application(factory);
        macApp.RenderUI();
    }
}
```

**Kết quả**:

```
Render Windows Button
Render Windows TextBox
Render Mac Button
Render Mac TextBox
```

**Giải thích**:

- `IUIFactory` định nghĩa các phương thức để tạo `Button` và `TextBox`.
- `WindowsUIFactory` và `MacUIFactory` tạo ra các sản phẩm cụ thể cho từng nền tảng.
- `Application` sử dụng factory để tạo và hiển thị giao diện, không cần biết chi tiết triển khai.

---

## 5. Decorator Design Pattern

**Decorator** là một mẫu thiết kế cấu trúc, cho phép mở rộng chức năng của một đối tượng mà không cần sửa đổi mã nguồn của nó.

### Ứng dụng

- Thêm các tính năng bổ sung cho đối tượng (ví dụ: thêm viền, màu sắc cho một thành phần giao diện).
- Linh hoạt hơn kế thừa, vì có thể thêm/xóa tính năng động.

### Ví dụ: Trang trí giao diện Button

**Cấu trúc**:

- **Component**: Giao diện hoặc lớp trừu tượng định nghĩa hành vi cơ bản.
- **Concrete Component**: Lớp cụ thể triển khai hành vi cơ bản.
- **Decorator**: Lớp trừu tượng giữ tham chiếu đến Component và triển khai cùng giao diện.
- **Concrete Decorator**: Thêm các tính năng bổ sung.

**Mã ví dụ**:

```csharp
using System;

// Component
public interface IButton
{
    string Render();
}

// Concrete Component
public class SimpleButton : IButton
{
    public string Render() => "Basic Button";
}

// Decorator
public abstract class ButtonDecorator : IButton
{
    protected IButton _button;

    public ButtonDecorator(IButton button)
    {
        _button = button;
    }

    public virtual string Render() => _button.Render();
}

// Concrete Decorators
public class BorderButtonDecorator : ButtonDecorator
{
    public BorderButtonDecorator(IButton button) : base(button) { }

    public override string Render() => $"{_button.Render()} with Border";
}

public class ColoredButtonDecorator : ButtonDecorator
{
    public ColoredButtonDecorator(IButton button) : base(button) { }

    public override string Render() => $"{_button.Render()} with Color";
}

class Program
{
    static void Main(string[] args)
    {
        // Tạo button cơ bản
        IButton button = new SimpleButton();
        Console.WriteLine(button.Render());

        // Thêm viền
        button = new BorderButtonDecorator(button);
        Console.WriteLine(button.Render());

        // Thêm màu
        button = new ColoredButtonDecorator(button);
        Console.WriteLine(button.Render());
    }
}
```

**Kết quả**:

```
Basic Button
Basic Button with Border
Basic Button with Border with Color
```

**Giải thích**:

- `SimpleButton` là button cơ bản.
- `BorderButtonDecorator` và `ColoredButtonDecorator` thêm các tính năng (viền, màu) mà không sửa đổi lớp `SimpleButton`.
- Có thể kết hợp nhiều decorator để mở rộng chức năng.

---

## 6. So sánh Abstract Factory và Decorator

| **Tiêu chí**       | **Abstract Factory**                     | **Decorator**                             |
| ------------------ | ---------------------------------------- | ----------------------------------------- |
| **Loại**           | Creational Pattern                       | Structural Pattern                        |
| **Mục đích**       | Tạo nhóm đối tượng liên quan             | Mở rộng chức năng của đối tượng           |
| **Cách hoạt động** | Sử dụng factory để tạo đối tượng         | Bọc đối tượng để thêm tính năng           |
| **Ứng dụng**       | Tạo giao diện cho các nền tảng khác nhau | Thêm tính năng động (viền, màu, hiệu ứng) |
| **Độ linh hoạt**   | Cố định các loại đối tượng được tạo      | Linh hoạt, có thể thêm/xóa tính năng      |

---

## 7. Bài tập thực hành bổ sung

1. **Unit Test**:

   - Viết unit test cho các phương thức của `StudentManager` để kiểm tra trường hợp danh sách rỗng hoặc tìm kiếm không có kết quả.
   - Sử dụng xUnit hoặc NUnit để kiểm tra.

2. **Abstract Factory**:

   - Tạo một hệ thống Abstract Factory để tạo các thành phần giao diện (Button, Checkbox) cho Android và iOS.

3. **Decorator**:
   - Mở rộng ví dụ Decorator để thêm tính năng Shadow (bóng) và Rounded (bo góc) cho Button.

---

## 8. Tài liệu tham khảo

- **Unit Testing**: Tài liệu chính thức của xUnit: https://xunit.net/
- **Design Patterns**: Sách "Design Patterns: Elements of Reusable Object-Oriented Software" của Gang of Four.
- **.NET Documentation**: https://learn.microsoft.com/en-us/dotnet/
