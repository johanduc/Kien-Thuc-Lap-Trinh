# Buổi 25: Attribute trong .NET

## 1. Tổng quan về Attribute

### 1.1. Attribute là gì?

- **Attribute** (thuộc tính) là một cơ chế trong .NET cho phép gắn thêm **metadata** (siêu dữ liệu) vào các thành phần chương trình như class, method, property, v.v.
- Metadata này cung cấp thông tin bổ sung cho compiler, runtime hoặc các công cụ khác để thay đổi hành vi hoặc cung cấp thông tin về mã nguồn.
- Attribute không thay đổi logic chính của chương trình mà chỉ cung cấp thông tin bổ sung.
- Ví dụ: Attribute có thể được dùng để đánh dấu một method là lỗi thời (`[Obsolete]`), kiểm tra dữ liệu đầu vào, hoặc cấu hình serialization.

### 1.2. Cú pháp sử dụng Attribute

- Attribute được khai báo bằng cách đặt tên attribute trong dấu ngoặc vuông `[]` trước thành phần cần gắn.
- Ví dụ:
  ```csharp
  [Obsolete("Phương thức này đã lỗi thời, vui lòng sử dụng phương thức mới")]
  public void OldMethod() { }
  ```
- Attribute có thể nhận tham số (constructor parameters) hoặc các thuộc tính (properties) để tùy chỉnh.

### 1.3. Một số Attribute phổ biến trong .NET

- `[Obsolete]`: Đánh dấu một thành phần là lỗi thời, cảnh báo người dùng khi sử dụng.
- `[Serializable]`: Cho phép một class có thể được serialize (chuyển thành dạng dữ liệu để lưu trữ hoặc truyền tải).
- `[DataContract]`, `[DataMember]`: Dùng trong WCF hoặc serialization để kiểm soát dữ liệu.
- `[Conditional]`: Quy định một method chỉ được gọi trong điều kiện cụ thể (thường dùng trong debug).
- Attribute tùy chỉnh: Người dùng có thể tự tạo Attribute để phục vụ mục đích riêng.

## 2. Cách Attribute hoạt động

### 2.1. Attribute và Reflection

- Attribute được truy xuất tại runtime thông qua **Reflection** (một cơ chế trong .NET cho phép kiểm tra và thao tác với metadata của chương trình).
- Reflection cho phép đọc thông tin từ Attribute và thay đổi hành vi của chương trình dựa trên metadata.
- Ví dụ: Kiểm tra xem một method có được gắn Attribute `[Obsolete]` hay không.

### 2.2. Cấu trúc của một Attribute

- Attribute là một class kế thừa từ lớp `System.Attribute`.
- Một Attribute có thể có:
  - **Constructor**: Nhận tham số để cấu hình Attribute.
  - **Properties/Fields**: Lưu trữ giá trị metadata.
- Attribute thường được đặt tên với hậu tố `Attribute`, nhưng khi sử dụng, có thể bỏ hậu tố này.
  - Ví dụ: `[Obsolete]` thay vì `[ObsoleteAttribute]`.

## 3. Ví dụ minh họa trong video

### 3.1. Sử dụng Attribute `[Obsolete]`

- Mục đích: Đánh dấu một phương thức/class là lỗi thời, đưa ra thông báo hoặc lỗi khi sử dụng.
- Ví dụ:

  ```csharp
  class Program
  {
      [Obsolete("Phương thức này đã lỗi thời, hãy sử dụng NewMethod thay thế")]
      public void OldMethod()
      {
          Console.WriteLine("Đây là phương thức cũ");
      }

      public void NewMethod()
      {
          Console.WriteLine("Đây là phương thức mới");
      }

      static void Main(string[] args)
      {
          Program p = new Program();
          p.OldMethod(); // Sẽ hiển thị cảnh báo trong IDE
          p.NewMethod();
      }
  }
  ```

- Kết quả:
  - IDE sẽ hiển thị cảnh báo rằng `OldMethod` đã lỗi thời.
  - Nếu thêm tham số `isError: true` vào `[Obsolete("...", true)]`, trình biên dịch sẽ báo lỗi và không cho phép sử dụng.

### 3.2. Tạo Attribute tùy chỉnh

- Mục đích: Tạo một Attribute để gắn metadata tùy chỉnh cho các thành phần chương trình.
- Ví dụ: Tạo Attribute để lưu thông tin tác giả của một class hoặc method.

  ```csharp
  // Định nghĩa Attribute tùy chỉnh
  [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = true)]
  public class AuthorAttribute : Attribute
  {
      public string Name { get; set; }
      public string Version { Eighteenth Century }

      public AuthorAttribute(string name, string version)
      {
          Name = name;
          Version = version;
      }
  }

  // Sử dụng Attribute
  [Author("Nguyen Van A", "1.0")]
  [Author("Tran Van B", "1.1")]
  class MyClass
  {
      [Author("Le Van C", "2.0")]
      public void MyMethod() { }
  }

  // Truy xuất Attribute bằng Reflection
  class Program
  {
      static void Main(string[] args)
      {
          // Lấy thông tin Attribute từ class
          var classAttributes = typeof(MyClass).GetCustomAttributes<AuthorAttribute>();
          foreach (var attr in classAttributes)
          {
              Console.WriteLine($"Author: {attr.Name}, Version: {attr.Version}");
          }

          // Lấy thông tin Attribute từ method
          var methodInfo = typeof(MyClass).GetMethod("MyMethod");
          var methodAttributes = methodInfo.GetCustomAttributes<AuthorAttribute>();
          foreach (var attr in methodAttributes)
          {
              Console.WriteLine($"Method Author: {attr.Name}, Version: {attr.Version}");
          }
      }
  }
  ```

- Giải thích:
  - `[AttributeUsage]`: Quy định Attribute có thể áp dụng cho `Class` hoặc `Method`, và cho phép gắn nhiều lần (`AllowMultiple = true`).
  - `AuthorAttribute`: Lưu trữ thông tin về tác giả và phiên bản.
  - Reflection được sử dụng để truy xuất thông tin Attribute tại runtime.

### 3.3. Sử dụng Attribute để kiểm tra dữ liệu

- Ví dụ: Tạo Attribute để kiểm tra giá trị đầu vào của một property.

  ```csharp
  [AttributeUsage(AttributeTargets.Property)]
  public class RangeAttribute : Attribute
  {
      public int Min { get; }
      public int Max { get; }

      public RangeAttribute(int min, int max)
      {
          Min = min;
          Max = max;
      }
  }

  class Person
  {
      private int age;
      [Range(0, 150)]
      public int Age
      {
          get => age;
          set
          {
              var range = typeof(Person).GetProperty("Age")
                          .GetCustomAttribute<RangeAttribute>();
              if (value < range.Min || value > range.Max)
                  throw new ArgumentException($"Age must be between {range.Min} and {range.Max}");
              age = value;
          }
      }
  }

  class Program
  {
      static void Main(string[] args)
      {
          try
          {
              Person p = new Person();
              p.Age = 200; // Sẽ ném ngoại lệ
          }
          catch (ArgumentException ex)
          {
              Console.WriteLine(ex.Message);
          }
      }
  }
  ```

- Kết quả:
  - Nếu gán giá trị `Age` ngoài khoảng `[0, 150]`, chương trình sẽ ném ngoại lệ với thông báo lỗi tương ứng.

## 4. Bài tập thực hành

1. **Bài tập 1**: Tạo một Attribute tùy chỉnh tên `DescriptionAttribute` để lưu mô tả cho một class hoặc method. Viết chương trình sử dụng Reflection để hiển thị mô tả của tất cả các class/method trong một assembly.
2. **Bài tập 2**: Tạo một Attribute để kiểm tra định dạng email của một property. Nếu email không hợp lệ, ném ngoại lệ.
3. **Bài tập 3**: Sử dụng Attribute `[Conditional("DEBUG")]` để chỉ gọi một phương thức trong chế độ Debug. Thử chạy chương trình ở cả chế độ Debug và Release để kiểm tra.

## 5. Một số lưu ý

- Attribute không nên chứa logic phức tạp, chỉ nên dùng để lưu metadata.
- Sử dụng Reflection cẩn thận vì có thể ảnh hưởng đến hiệu năng.
- Attribute là công cụ mạnh mẽ trong các framework như ASP.NET, Entity Framework để cấu hình hành vi (ví dụ: `[Required]`, `[Key]` trong EF).

## 6. Tài liệu tham khảo

- Tài liệu chính thức của Microsoft về Attribute: [Attributes in C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/attributes/)
- Video: Học lập trình .NET - Phần 25 - Attribute (https://www.youtube.com/watch?v=dHZo6Y2Vnng)
