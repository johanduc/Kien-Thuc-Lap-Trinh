# Buổi 11: Lập trình hướng đối tượng trong .NET

Dưới đây là ghi chép và tổng hợp đầy đủ các kiến thức, ví dụ và bài tập từ video YouTube: [Học lập trình .NET - Phần 11 - Lập trình hướng đối tượng trong .NET](https://www.youtube.com/watch?v=oiQIS1sFFi0&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=11). Nội dung được trình bày rõ ràng, chi tiết và có cấu trúc để dễ dàng ôn tập và thực hành.

---

## 1. Tổng quan về lập trình hướng đối tượng (OOP)

Lập trình hướng đối tượng (OOP - Object-Oriented Programming) là một phương pháp lập trình dựa trên các **đối tượng** (objects) để mô hình hóa các thực thể trong thế giới thực. Trong .NET, OOP được sử dụng rộng rãi để xây dựng các ứng dụng có tính tái sử dụng, dễ bảo trì và mở rộng.

### Các đặc trưng cơ bản của OOP

1. **Tính đóng gói (Encapsulation)**:

   - Giấu thông tin chi tiết bên trong đối tượng, chỉ cung cấp các phương thức công khai (public) để giao tiếp.
   - Được thực hiện thông qua các từ khóa `private`, `protected`, `public`.

2. **Tính kế thừa (Inheritance)**:

   - Cho phép một lớp con (derived class) thừa hưởng các thuộc tính và phương thức từ lớp cha (base class).
   - Trong C#, sử dụng từ khóa `:` để kế thừa.

3. **Tính đa hình (Polymorphism)**:

   - Cho phép các đối tượng thuộc các lớp khác nhau được gọi thông qua cùng một giao diện hoặc phương thức, nhưng có hành vi khác nhau.
   - Có hai loại: đa hình tĩnh (overloading) và đa hình động (overriding).

4. **Tính trừu tượng (Abstraction)**:
   - Chỉ hiển thị các chi tiết cần thiết và ẩn các chi tiết không quan trọng.
   - Được thực hiện thông qua lớp trừu tượng (`abstract class`) hoặc giao diện (`interface`).

---

## 2. Các thành phần cơ bản trong OOP với C#

### 2.1. Class và Object

- **Class**: Là bản thiết kế (blueprint) để tạo ra các đối tượng. Một class bao gồm các thuộc tính (fields, properties) và phương thức (methods).
- **Object**: Là một thể hiện cụ thể (instance) của class.

**Ví dụ**:

```csharp
// Định nghĩa class
public class Person
{
    // Thuộc tính
    public string Name { get; set; }
    public int Age { get; set; }

    // Phương thức
    public void DisplayInfo()
    {
        Console.WriteLine($"Name: {Name}, Age: {Age}");
    }
}

// Tạo đối tượng
Person person = new Person();
person.Name = "Nguyen Van A";
person.Age = 25;
person.DisplayInfo();
```

**Giải thích**:

- `Person` là một class với hai thuộc tính (`Name`, `Age`) và một phương thức (`DisplayInfo`).
- `new Person()` tạo một đối tượng từ class `Person`.

### 2.2. Constructor

- Constructor là phương thức đặc biệt được gọi khi tạo đối tượng.
- Có thể có nhiều constructor (constructor overloading).

**Ví dụ**:

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    // Constructor mặc định
    public Person()
    {
        Name = "Unknown";
        Age = 0;
    }

    // Constructor có tham số
    public Person(string name, int age)
    {
        Name = name;
        Age = age;
    }

    public void DisplayInfo()
    {
        Console.WriteLine($"Name: {Name}, Age: {Age}");
    }
}

// Sử dụng
Person person1 = new Person(); // Gọi constructor mặc định
Person person2 = new Person("Nguyen Van B", 30); // Gọi constructor có tham số
person1.DisplayInfo();
person2.DisplayInfo();
```

### 2.3. Tính đóng gói

- Sử dụng các access modifiers (`public`, `private`, `protected`, `internal`) để kiểm soát quyền truy cập.
- Sử dụng `properties` để kiểm soát việc đọc/ghi dữ liệu.

**Ví dụ**:

```csharp
public class Employee
{
    private string _name;
    private double _salary;

    public string Name
    {
        get { return _name; }
        set { _name = value; }
    }

    public double Salary
    {
        get { return _salary; }
        set
        {
            if (value > 0)
                _salary = value;
            else
                _salary = 0;
        }
    }
}
```

**Giải thích**:

- Thuộc tính `_name` và `_salary` được khai báo `private` để hạn chế truy cập trực tiếp.
- Property `Name` và `Salary` cung cấp cách an toàn để truy cập và thay đổi giá trị.

### 2.4. Tính kế thừa

- Một lớp con có thể kế thừa từ một lớp cha bằng từ khóa `:`.
- Lớp con có thể gọi constructor của lớp cha bằng từ khóa `base`.

**Ví dụ**:

```csharp
public class Animal
{
    public string Name { get; set; }

    public Animal(string name)
    {
        Name = name;
    }

    public virtual void Speak()
    {
        Console.WriteLine($"{Name} makes a sound.");
    }
}

public class Dog : Animal
{
    public Dog(string name) : base(name)
    {
    }

    public override void Speak()
    {
        Console.WriteLine($"{Name} barks: Woof!");
    }
}

// Sử dụng
Dog dog = new Dog("Buddy");
dog.Speak(); // Output: Buddy barks: Woof!
```

**Giải thích**:

- `Dog` kế thừa từ `Animal` và ghi đè (override) phương thức `Speak`.

### 2.5. Tính đa hình

- **Đa hình tĩnh (Overloading)**: Nhiều phương thức cùng tên nhưng tham số khác nhau.
- **Đa hình động (Overriding)**: Ghi đè phương thức của lớp cha bằng từ khóa `override`.

**Ví dụ (Overloading)**:

```csharp
public class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }

    public double Add(double a, double b)
    {
        return a + b;
    }
}
```

**Ví dụ (Overriding)**:

- Xem lại ví dụ về `Animal` và `Dog` ở trên.

### 2.6. Tính trừu tượng

- **Abstract Class**: Lớp trừu tượng không thể tạo đối tượng trực tiếp, dùng để định nghĩa các phương thức chung.
- **Interface**: Định nghĩa hợp đồng (contract) mà các lớp phải tuân theo.

**Ví dụ (Abstract Class)**:

```csharp
public abstract class Shape
{
    public abstract double CalculateArea();
}

public class Circle : Shape
{
    public double Radius { get; set; }

    public Circle(double radius)
    {
        Radius = radius;
    }

    public override double CalculateArea()
    {
        return Math.PI * Radius * Radius;
    }
}
```

**Ví dụ (Interface)**:

```csharp
public interface IShape
{
    double CalculateArea();
}

public class Rectangle : IShape
{
    public double Width { get; set; }
    public double Height { get; set; }

    public Rectangle(double width, double height)
    {
        Width = width;
        Height = height;
    }

    public double CalculateArea()
    {
        return Width * Height;
    }
}
```

---

## 3. Bài tập ví dụ

### Bài tập 1: Xây dựng lớp `Student`

- Tạo lớp `Student` với các thuộc tính: `StudentId`, `FullName`, `GPA`.
- Thêm constructor và phương thức hiển thị thông tin.
- Đảm bảo `GPA` chỉ nhận giá trị từ 0 đến 10.

**Giải bài tập**:

```csharp
public class Student
{
    private string _studentId;
    private string _fullName;
    private double _gpa;

    public string StudentId
    {
        get { return _studentId; }
        set { _studentId = value; }
    }

    public string FullName
    {
        get { return _fullName; }
        set { _fullName = value; }
    }

    public double GPA
    {
        get { return _gpa; }
        set
        {
            if (value >= 0 && value <= 10)
                _gpa = value;
            else
                _gpa = 0;
        }
    }

    public Student(string studentId, string fullName, double gpa)
    {
        StudentId = studentId;
        FullName = fullName;
        GPA = gpa;
    }

    public void DisplayInfo()
    {
        Console.WriteLine($"Student ID: {StudentId}, Full Name: {FullName}, GPA: {GPA}");
    }
}

// Sử dụng
Student student = new Student("S001", "Nguyen Van C", 8.5);
student.DisplayInfo();
```

### Bài tập 2: Tính kế thừa và đa hình

- Tạo lớp cha `Vehicle` với phương thức `Move`.
- Tạo lớp con `Car` và `Bicycle`, ghi đè phương thức `Move`.

**Giải bài tập**:

```csharp
public class Vehicle
{
    public string Brand { get; set; }

    public Vehicle(string brand)
    {
        Brand = brand;
    }

    public virtual void Move()
    {
        Console.WriteLine($"{Brand} is moving.");
    }
}

public class Car : Vehicle
{
    public Car(string brand) : base(brand)
    {
    }

    public override void Move()
    {
        Console.WriteLine($"{Brand} car is driving on the road.");
    }
}

public class Bicycle : Vehicle
{
    public Bicycle(string brand) : base(brand)
    {
    }

    public override void Move()
    {
        Console.WriteLine($"{Brand} bicycle is pedaling.");
    }
}

// Sử dụng
Vehicle car = new Car("Toyota");
Vehicle bicycle = new Bicycle("Giant");
car.Move(); // Output: Toyota car is driving on the road.
bicycle.Move(); // Output: Giant bicycle is pedaling.
```

### Bài tập 3: Interface

- Tạo interface `IPrintable` với phương thức `Print`.
- Tạo lớp `Document` triển khai interface này.

**Giải bài tập**:

```csharp
public interface IPrintable
{
    void Print();
}

public class Document : IPrintable
{
    public string Title { get; set; }

    public Document(string title)
    {
        Title = title;
    }

    public void Print()
    {
        Console.WriteLine($"Printing document: {Title}");
    }
}

// Sử dụng
Document doc = new Document("My Report");
doc.Print(); // Output: Printing document: My Report
```

---

## 4. Tổng kết

- OOP trong .NET giúp tổ chức code theo cách mô phỏng thế giới thực, tăng tính tái sử dụng và dễ bảo trì.
- Các đặc trưng chính: đóng gói, kế thừa, đa hình, trừu tượng.
- C# cung cấp các công cụ mạnh mẽ như class, interface, abstract class để triển khai OOP.
- Các bài tập ví dụ giúp củng cố kiến thức về cách xây dựng và sử dụng các thành phần OOP.
