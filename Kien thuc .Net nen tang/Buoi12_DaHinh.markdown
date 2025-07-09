# Buổi 12: Đa Hình

## 1. Mục tiêu bài học

- Hiểu khái niệm đa hình (Polymorphism) trong lập trình hướng đối tượng.
- Nắm vững hai loại đa hình: **Static Polymorphism** (đa hình tĩnh) và **Dynamic Polymorphism** (đa hình động).
- Ứng dụng đa hình trong các tình huống thực tế thông qua ví dụ và bài tập.

## 2. Nội dung chính

### 2.1. Khái niệm đa hình

- **Đa hình** (Polymorphism) là một trong bốn tính chất cốt lõi của lập trình hướng đối tượng (OOP), bên cạnh kế thừa, đóng gói, và trừu tượng.
- Đa hình cho phép một hành động được thực hiện theo nhiều cách khác nhau tùy thuộc vào đối tượng gọi hành động đó.
- Có hai loại đa hình:
  - **Static Polymorphism** (Đa hình tĩnh): Xảy ra tại thời điểm biên dịch (compile-time), ví dụ: nạp chồng hàm (method overloading).
  - **Dynamic Polymorphism** (Đa hình động): Xảy ra tại thời điểm chạy (run-time), ví dụ: ghi đè hàm (method overriding).

### 2.2. Static Polymorphism (Đa hình tĩnh)

- **Đặc điểm**:
  - Xác định tại thời điểm biên dịch.
  - Thường được thực hiện thông qua **method overloading** hoặc **operator overloading**.
  - Các phương thức có cùng tên nhưng khác tham số (số lượng, kiểu dữ liệu, hoặc thứ tự tham số).
- **Ví dụ**:

  ```csharp
  class Calculator
  {
      public int Add(int a, int b)
      {
          return a + b;
      }

      public double Add(double a, double b)
      {
          return a + b;
      }

      public int Add(int a, int b, int c)
      {
          return a + b + c;
      }
  }
  ```

  - Trong ví dụ trên, phương thức `Add` được nạp chồng với các tham số khác nhau.
  - Trình biên dịch sẽ chọn phương thức phù hợp dựa trên kiểu và số lượng tham số khi gọi.

### 2.3. Dynamic Polymorphism (Đa hình động)

- **Đặc điểm**:
  - Xác định tại thời điểm chạy.
  - Thường được thực hiện thông qua **method overriding** (ghi đè phương thức) và sử dụng từ khóa `virtual`/`override`.
  - Một lớp con có thể cung cấp triển khai khác cho phương thức được định nghĩa trong lớp cha.
- **Ví dụ**:

  ```csharp
  class Animal
  {
      public virtual void MakeSound()
      {
          Console.WriteLine("Some generic sound");
      }
  }

  class Dog : Animal
  {
      public override void MakeSound()
      {
          Console.WriteLine("Woof woof!");
      }
  }

  class Cat : Animal
  {
      public override void MakeSound()
      {
          Console.WriteLine("Meow meow!");
      }
  }
  ```

  - Trong ví dụ trên, phương thức `MakeSound` được đánh dấu `virtual` trong lớp cha (`Animal`) và được ghi đè (`override`) trong các lớp con (`Dog`, `Cat`).
  - Khi gọi `MakeSound` trên một đối tượng kiểu `Animal`, phương thức cụ thể của lớp con sẽ được thực thi.

### 2.4. Sử dụng đa hình trong thực tế

- **Tình huống**: Một ứng dụng quản lý các hình học (Shape) như hình tròn, hình vuông, hình chữ nhật. Mỗi hình có cách tính diện tích khác nhau.
- **Cách triển khai**:
  - Tạo một lớp trừu tượng hoặc giao diện (interface) để định nghĩa phương thức tính diện tích.
  - Các lớp con sẽ triển khai phương thức này theo cách riêng.
- **Ví dụ**:

  ```csharp
  abstract class Shape
  {
      public abstract double CalculateArea();
  }

  class Circle : Shape
  {
      private double radius;

      public Circle(double radius)
      {
          this.radius = radius;
      }

      public override double CalculateArea()
      {
          return Math.PI * radius * radius;
      }
  }

  class Rectangle : Shape
  {
      private double width, height;

      public Rectangle(double width, double height)
      {
          this.width = width;
          this.height = height;
      }

      public override double CalculateArea()
      {
          return width * height;
      }
  }
  ```

### 2.5. Giao diện (Interface) và đa hình

- Giao diện (interface) là một cách khác để đạt được đa hình động.
- Các lớp triển khai giao diện phải cung cấp định nghĩa cho các phương thức được khai báo trong giao diện.
- **Ví dụ**:

  ```csharp
  interface IShape
  {
      double CalculateArea();
  }

  class Circle : IShape
  {
      private double radius;

      public Circle(double radius)
      {
          this.radius = radius;
      }

      public double CalculateArea()
      {
          return Math.PI * radius * radius;
      }
  }

  class Rectangle : IShape
  {
      private double width, height;

      public Rectangle(double width, double height)
      {
          this.width = width;
          this.height = height;
      }

      public double CalculateArea()
      {
          return width * height;
      }
  }
  ```

### 2.6. Ứng dụng thực tế

- **Đa hình tĩnh**: Sử dụng khi cần cung cấp nhiều cách thực hiện một hành động dựa trên tham số đầu vào (ví dụ: các hàm khởi tạo khác nhau).
- **Đa hình động**: Sử dụng khi cần linh hoạt thay đổi hành vi của đối tượng tại thời điểm chạy (ví dụ: các hành vi khác nhau của các loại nhân viên trong hệ thống quản lý nhân sự).

## 3. Bài tập ví dụ

### Bài tập 1: Tính diện tích các hình học

- Yêu cầu: Viết chương trình sử dụng đa hình để tính diện tích của các hình: hình tròn, hình vuông, hình chữ nhật.
- **Giải pháp**:

  ```csharp
  using System;

  abstract class Shape
  {
      public abstract double CalculateArea();
  }

  class Circle : Shape
  {
      private double radius;

      public Circle(double radius)
      {
          this.radius = radius;
      }

      public override double CalculateArea()
      {
          return Math.PI * radius * radius;
      }
  }

  class Square : Shape
  {
      private double side;

      public Square(double side)
      {
          this.side = side;
      }

      public override double CalculateArea()
      {
          return side * side;
      }
  }

  class Rectangle : Shape
  {
      private double width, height;

      public Rectangle(double width, double height)
      {
          this.width = width;
          this.height = height;
      }

      public override double CalculateArea()
      {
          return width * height;
      }
  }

  class Program
  {
      static void Main(string[] args)
      {
          Shape[] shapes = new Shape[]
          {
              new Circle(5),
              new Square(4),
              new Rectangle(3, 6)
          };

          foreach (Shape shape in shapes)
          {
              Console.WriteLine($"Area of {shape.GetType().Name}: {shape.CalculateArea()}");
          }
      }
  }
  ```

- **Kết quả**:
  ```
  Area of Circle: 78.53981633974483
  Area of Square: 16
  Area of Rectangle: 18
  ```

### Bài tập 2: Hệ thống quản lý nhân viên

- Yêu cầu: Xây dựng hệ thống quản lý nhân viên với các loại nhân viên: Nhân viên full-time, nhân viên part-time. Mỗi loại có cách tính lương khác nhau.
- **Giải pháp**:

  ```csharp
  using System;

  abstract class Employee
  {
      public string Name { get; set; }
      public abstract double CalculateSalary();
  }

  class FullTimeEmployee : Employee
  {
      private double monthlySalary;

      public FullTimeEmployee(string name, double monthlySalary)
      {
          Name = name;
          this.monthlySalary = monthlySalary;
      }

      public override double CalculateSalary()
      {
          return monthlySalary;
      }
  }

  class PartTimeEmployee : Employee
  {
      private double hourlyRate;
      private double hoursWorked;

      public PartTimeEmployee(string name, double hourlyRate, double hoursWorked)
      {
          Name = name;
          this.hourlyRate = hourlyRate;
          this.hoursWorked = hoursWorked;
      }

      public override double CalculateSalary()
      {
          return hourlyRate * hoursWorked;
      }
  }

  class Program
  {
      static void Main(string[] args)
      {
          Employee[] employees = new Employee[]
          {
              new FullTimeEmployee("Nguyen Van A", 5000000),
              new PartTimeEmployee("Tran Thi B", 50000, 80)
          };

          foreach (Employee emp in employees)
          {
              Console.WriteLine($"Salary of {emp.Name}: {emp.CalculateSalary()}");
          }
      }
  }
  ```

- **Kết quả**:
  ```
  Salary of Nguyen Van A: 5000000
  Salary of Tran Thi B: 4000000
  ```

## 4. Tóm tắt

- **Đa hình** là một tính chất quan trọng trong OOP, cho phép thực hiện một hành động theo nhiều cách khác nhau.
- **Static Polymorphism** sử dụng nạp chồng hàm, xác định tại thời điểm biên dịch.
- **Dynamic Polymorphism** sử dụng ghi đè phương thức hoặc giao diện, xác định tại thời điểm chạy.
- Ứng dụng đa hình giúp mã nguồn linh hoạt, dễ mở rộng và bảo trì.

## 5. Tài liệu tham khảo

- Video: [Học lập trình .NET - Phần 12 - Đa hình](https://www.youtube.com/watch?v=yhgaUBYNGpw&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=12)
- Tài liệu .NET: [Microsoft Docs - Polymorphism](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/polymorphism)
