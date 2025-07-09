# Buổi 26: Sử dụng Configuration trong .NET

## Mục tiêu

- Hiểu khái niệm Configuration trong .NET
- Tìm hiểu cách sử dụng Configuration để quản lý cấu hình ứng dụng
- Thực hành đọc và sử dụng cấu hình từ các nguồn khác nhau (appsettings.json, biến môi trường, dòng lệnh)
- Làm quen với các kỹ thuật binding configuration vào đối tượng

## Nội dung chính

### 1. Tổng quan về Configuration

- **Configuration** là cách quản lý các thiết lập cấu hình (settings) của ứng dụng như chuỗi kết nối cơ sở dữ liệu, API keys, hoặc các thông số khác.
- Trong .NET, hệ thống Configuration được xây dựng linh hoạt, hỗ trợ nhiều nguồn cấu hình:
  - Tệp JSON (appsettings.json)
  - Biến môi trường (Environment Variables)
  - Tham số dòng lệnh (Command-line arguments)
  - Các nguồn tùy chỉnh khác
- Configuration trong .NET Core/5/6/7/8 sử dụng **IConfiguration** interface để truy xuất dữ liệu.

### 2. Các thành phần chính

- **IConfiguration**: Interface chính để truy cập cấu hình.
- **ConfigurationBuilder**: Lớp dùng để xây dựng cấu hình từ nhiều nguồn.
- **appsettings.json**: Tệp cấu hình mặc định, thường được sử dụng để lưu trữ các thiết lập tĩnh.
- **Dependency Injection (DI)**: Configuration thường được inject vào các dịch vụ hoặc controller.

### 3. Thiết lập Configuration

#### Bước 1: Tạo tệp appsettings.json

- Tạo tệp `appsettings.json` trong dự án với nội dung ví dụ:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MyApp;Trusted_Connection=True;"
  },
  "AppSettings": {
    "ApiKey": "my-secret-api-key",
    "MaxItems": 100
  }
}
```

#### Bước 2: Cấu hình trong Program.cs

- Sử dụng `ConfigurationBuilder` để đọc cấu hình từ `appsettings.json`:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Cấu hình dịch vụ
builder.Services.AddControllers();

// Đọc cấu hình từ appsettings.json
var configuration = builder.Configuration;

var app = builder.Build();
app.UseRouting();
app.MapControllers();
app.Run();
```

#### Bước 3: Đọc cấu hình

- Đọc giá trị từ `appsettings.json`:

```csharp
string connectionString = configuration.GetConnectionString("DefaultConnection");
string apiKey = configuration["AppSettings:ApiKey"];
int maxItems = configuration.GetValue<int>("AppSettings:MaxItems");
```

### 4. Binding Configuration vào đối tượng

- Tạo lớp để ánh xạ cấu hình:

```csharp
public class AppSettings
{
    public string ApiKey { get; set; }
    public int MaxItems { get; set; }
}
```

- Binding cấu hình vào đối tượng:

```csharp
var appSettings = configuration.GetSection("AppSettings").Get<AppSettings>();
Console.WriteLine($"ApiKey: {appSettings.ApiKey}, MaxItems: {appSettings.MaxItems}");
```

- Inject `AppSettings` vào dịch vụ sử dụng DI:

```csharp
builder.Services.Configure<AppSettings>(configuration.GetSection("AppSettings"));
```

- Sử dụng trong Controller:

```csharp
public class HomeController : Controller
{
    private readonly AppSettings _appSettings;

    public HomeController(IOptions<AppSettings> options)
    {
        _appSettings = options.Value;
    }

    public IActionResult Index()
    {
        ViewData["ApiKey"] = _appSettings.ApiKey;
        return View();
    }
}
```

### 5. Sử dụng biến môi trường và tham số dòng lệnh

- Thêm cấu hình từ biến môi trường:

```csharp
builder.Configuration.AddEnvironmentVariables();
```

- Thêm cấu hình từ tham số dòng lệnh:

```csharp
builder.Configuration.AddCommandLine(args);
```

- Ưu tiên cấu hình: Tham số dòng lệnh > Biến môi trường > appsettings.json

### 6. Sử dụng appsettings cho các môi trường khác nhau

- Tạo các tệp `appsettings.Development.json`, `appsettings.Production.json`:

```json
// appsettings.Development.json
{
  "AppSettings": {
    "ApiKey": "dev-api-key",
    "MaxItems": 50
  }
}
```

- .NET tự động chọn tệp dựa trên môi trường (`ASPNETCORE_ENVIRONMENT`).

### 7. Bài tập ví dụ

#### Bài tập 1: Đọc cấu hình từ appsettings.json

- Tạo dự án Console App.
- Thêm tệp `appsettings.json`:

```json
{
  "AppSettings": {
    "AppName": "MyConsoleApp",
    "Version": "1.0.0"
  }
}
```

- Đọc và hiển thị cấu hình:

```csharp
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

class Program
{
    static void Main(string[] args)
    {
        // Tạo Configuration
        IConfiguration configuration = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
            .Build();

        // Đọc cấu hình
        string appName = configuration["AppSettings:AppName"];
        string version = configuration["AppSettings:Version"];
        Console.WriteLine($"App Name: {appName}, Version: {version}");
    }
}
```

#### Bài tập 2: Binding cấu hình vào đối tượng

- Tạo lớp `AppSettings`:

```csharp
public class AppSettings
{
    public string AppName { get; set; }
    public string Version { get; set; }
}
```

- Binding và hiển thị:

```csharp
var appSettings = configuration.GetSection("AppSettings").Get<AppSettings>();
Console.WriteLine($"App Name: {appSettings.AppName}, Version: {appSettings.Version}");
```

#### Bài tập 3: Sử dụng DI trong ASP.NET Core

- Tạo dự án ASP.NET Core Web API.
- Đăng ký `AppSettings` vào DI:

```csharp
builder.Services.Configure<AppSettings>(configuration.GetSection("AppSettings"));
```

- Sử dụng trong Controller:

```csharp
[ApiController]
[Route("api/[controller]")]
public class ConfigController : ControllerBase
{
    private readonly AppSettings _appSettings;

    public ConfigController(IOptions<AppSettings> options)
    {
        _appSettings = options.Value;
    }

    [HttpGet]
    public IActionResult Get()
    {
        return Ok(new { _appSettings.AppName, _appSettings.Version });
    }
}
```

### 8. Lưu ý

- Đảm bảo tệp `appsettings.json` được copy vào output directory (Properties: Copy to Output Directory = Copy always).
- Sử dụng `reloadOnChange: true` để tự động cập nhật cấu hình khi tệp thay đổi.
- Tránh lưu thông tin nhạy cảm (như API keys) trong mã nguồn, thay vào đó sử dụng biến môi trường hoặc Azure Key Vault.

### 9. Tài liệu tham khảo

- Microsoft Docs: [Configuration in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/)
- Video YouTube: [Học lập trình .NET - Phần 26 - Sử dụng Configuration](https://www.youtube.com/watch?v=iOZWVe_kbFg&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=26)
