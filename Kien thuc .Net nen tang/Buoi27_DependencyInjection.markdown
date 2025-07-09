# Buổi 27: Dependency Injection

## Mục tiêu

- Hiểu khái niệm Dependency Injection (DI) trong lập trình .NET.
- Nắm được cách triển khai DI trong ASP.NET Core.
- Thực hành xây dựng ứng dụng sử dụng DI để quản lý các phụ thuộc.

## Nội dung chính

### 1. Dependency Injection là gì?

- **Định nghĩa**: Dependency Injection là một mẫu thiết kế (Design Pattern) thuộc nhóm Inversion of Control (IoC), giúp giảm sự phụ thuộc trực tiếp giữa các lớp bằng cách truyền các phụ thuộc (dependencies) từ bên ngoài vào lớp sử dụng chúng.
- **Lợi ích**:
  - Giảm sự kết dính (decoupling) giữa các lớp.
  - Dễ dàng kiểm thử (unit test) vì có thể thay thế các phụ thuộc bằng các mock object.
  - Tăng tính tái sử dụng và bảo trì mã nguồn.
- **Nguyên tắc hoạt động**:
  - Thay vì một lớp tự tạo ra các phụ thuộc, các phụ thuộc sẽ được "tiêm" (inject) vào lớp qua constructor, property, hoặc method.

### 2. Các loại Dependency Injection

- **Constructor Injection**: Truyền phụ thuộc qua constructor của lớp.
- **Property Injection**: Truyền phụ thuộc qua thuộc tính (property).
- **Method Injection**: Truyền phụ thuộc qua tham số của phương thức.

### 3. Dependency Injection trong ASP.NET Core

- ASP.NET Core tích hợp sẵn DI Container (Service Container) để quản lý các dịch vụ (services).
- **Các bước cơ bản**:
  1. Đăng ký dịch vụ vào container trong phương thức `ConfigureServices` của lớp `Startup` hoặc `Program.cs` (tùy phiên bản .NET).
  2. Tiêm dịch vụ vào các lớp sử dụng thông qua constructor.
- **Vòng đời của dịch vụ (Service Lifetime)**:
  - **Transient**: Tạo mới mỗi lần được yêu cầu.
  - **Scoped**: Tạo một lần duy nhất trong một phạm vi (scope, ví dụ: một HTTP request).
  - **Singleton**: Tạo một lần duy nhất và chia sẻ cho toàn bộ ứng dụng.

### 4. Ví dụ thực hành

#### 4.1. Tạo dự án ASP.NET Core

- Tạo một dự án ASP.NET Core MVC mới:
  ```bash
  dotnet new mvc -o DependencyInjectionDemo
  cd DependencyInjectionDemo
  ```
- Cấu trúc dự án:
  - `Controllers`: Chứa các controller.
  - `Services`: Chứa các dịch vụ (services) để triển khai logic.
  - `Program.cs`: Nơi đăng ký các dịch vụ.

#### 4.2. Tạo dịch vụ

- Tạo interface `IMessageService` trong thư mục `Services`:
  ```csharp
  namespace DependencyInjectionDemo.Services
  {
      public interface IMessageService
      {
          string GetMessage();
      }
  }
  ```
- Tạo lớp triển khai `MessageService`:
  ```csharp
  namespace DependencyInjectionDemo.Services
  {
      public class MessageService : IMessageService
      {
          public string GetMessage()
          {
              return "Hello from MessageService!";
          }
      }
  }
  ```

#### 4.3. Đăng ký dịch vụ

- Trong `Program.cs`, thêm dịch vụ vào container:

  ```csharp
  var builder = WebApplication.CreateBuilder(args);

  // Add services to the container.
  builder.Services.AddControllersWithViews();
  builder.Services.AddTransient<IMessageService, MessageService>();

  var app = builder.Build();

  // Configure the HTTP request pipeline.
  if (!app.Environment.IsDevelopment())
  {
      app.UseExceptionHandler("/Home/Error");
      app.UseHsts();
  }

  app.UseHttpsRedirection();
  app.UseStaticFiles();
  app.UseRouting();
  app.UseAuthorization();
  app.MapControllerRoute(
      name: "default",
      pattern: "{controller=Home}/{action=Index}/{id?}");

  app.Run();
  ```

#### 4.4. Sử dụng dịch vụ trong Controller

- Tạo controller `HomeController` và tiêm `IMessageService` qua constructor:

  ```csharp
  using DependencyInjectionDemo.Services;
  using Microsoft.AspNetCore.Mvc;

  namespace DependencyInjectionDemo.Controllers
  {
      public class HomeController : Controller
      {
          private readonly IMessageService _messageService;

          public HomeController(IMessageService messageService)
          {
              _messageService = messageService;
          }

          public IActionResult Index()
          {
              var message = _messageService.GetMessage();
              ViewData["Message"] = message;
              return View();
          }
      }
  }
  ```

#### 4.5. Cập nhật View

- Chỉnh sửa `Views/Home/Index.cshtml` để hiển thị thông điệp:

  ```cshtml
  @{
      ViewData["Title"] = "Home Page";
  }

  <div class="text-center">
      <h1 class="display-4">Welcome</h1>
      <p>@ViewData["Message"]</p>
  </div>
  ```

#### 4.6. Chạy ứng dụng

- Chạy dự án:
  ```bash
  dotnet run
  ```
- Truy cập `https://localhost:5001`, bạn sẽ thấy thông điệp: "Hello from MessageService!".

### 5. Bài tập thực hành

#### Bài tập 1: Thêm dịch vụ mới

- Tạo một interface `IUserService` và lớp triển khai `UserService` trả về danh sách người dùng (dữ liệu giả lập).
- Đăng ký `IUserService` với vòng đời `Scoped`.
- Tiêm `IUserService` vào `HomeController` và hiển thị danh sách người dùng trên view.

**Gợi ý**:

- Interface `IUserService`:
  ```csharp
  public interface IUserService
  {
      List<string> GetUsers();
  }
  ```
- Lớp `UserService`:
  ```csharp
  public class UserService : IUserService
  {
      public List<string> GetUsers()
      {
          return new List<string> { "Alice", "Bob", "Charlie" };
      }
  }
  ```
- Đăng ký trong `Program.cs`:
  ```csharp
  builder.Services.AddScoped<IUserService, UserService>();
  ```

#### Bài tập 2: Thử nghiệm với các vòng đời khác nhau

- Thay đổi vòng đời của `IMessageService` thành `Singleton` và `Scoped`, sau đó kiểm tra sự khác biệt khi truy cập ứng dụng từ nhiều tab trình duyệt.

### 6. Một số lưu ý

- **Tránh sử dụng DI thủ công**: Không tự tạo các phụ thuộc trong lớp, hãy để DI Container quản lý.
- **Quản lý vòng đời cẩn thận**: Sử dụng vòng đời phù hợp để tránh lỗi như giữ trạng thái không mong muốn (đặc biệt với `Singleton`).
- **Sử dụng interface**: Luôn sử dụng interface thay vì lớp cụ thể để tăng tính linh hoạt.

### 7. Tài liệu tham khảo

- [Tài liệu chính thức ASP.NET Core DI](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)
- [Hướng dẫn IoC và DI](https://martinfowler.com/articles/injection.html)
