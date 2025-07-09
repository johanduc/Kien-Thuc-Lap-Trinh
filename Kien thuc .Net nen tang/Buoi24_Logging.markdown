# Buổi 24: Logging trong lập trình .NET

## Mục tiêu

- Hiểu khái niệm logging và vai trò của nó trong ứng dụng.
- Tìm hiểu cách sử dụng các thư viện logging tích hợp trong .NET.
- Thực hành triển khai logging với các ví dụ cụ thể.

## Nội dung chính

### 1. Logging là gì?

- **Logging** là quá trình ghi lại thông tin về hoạt động của ứng dụng trong quá trình chạy, giúp:
  - Theo dõi lỗi (errors).
  - Giám sát hiệu suất.
  - Gỡ lỗi (debugging).
  - Thu thập thông tin để phân tích hành vi ứng dụng.
- Các mức độ log phổ biến:
  - **Trace**: Thông tin chi tiết nhất, dùng để debug.
  - **Debug**: Thông tin phục vụ lập trình viên khi phát triển.
  - **Information**: Thông tin chung về hoạt động của ứng dụng.
  - **Warning**: Cảnh báo về các vấn đề tiềm ẩn.
  - **Error**: Lỗi xảy ra trong ứng dụng.
  - **Critical**: Lỗi nghiêm trọng, có thể khiến ứng dụng dừng hoạt động.

### 2. Logging trong .NET

- .NET cung cấp thư viện logging tích hợp sẵn: `Microsoft.Extensions.Logging`.
- Các thành phần chính:
  - **ILogger**: Giao diện để ghi log.
  - **ILoggerFactory**: Tạo các instance của ILogger.
  - **ILoggerProvider**: Nhà cung cấp logging (console, file, database, v.v.).
- Một số provider phổ biến:
  - **Console Provider**: Ghi log ra console.
  - **File Provider** (thư viện bên thứ ba như Serilog, NLog).
  - **Event Log Provider**: Ghi log vào Windows Event Log.
  - **Debug Provider**: Ghi log vào output window của IDE.

### 3. Cấu hình Logging trong .NET

- Logging được cấu hình thông qua Dependency Injection (DI) trong ứng dụng ASP.NET Core.
- Các bước cơ bản:
  1. Thêm dịch vụ logging vào container DI trong `Program.cs` hoặc `Startup.cs`.
  2. Inject `ILogger<T>` vào các class cần sử dụng logging.
  3. Sử dụng các phương thức của ILogger để ghi log.

### 4. Ví dụ thực hành

Dưới đây là các ví dụ thực hành được trình bày trong video.

#### Ví dụ 1: Logging cơ bản với Console Provider

Mục tiêu: Tạo một ứng dụng console đơn giản để ghi log ra console.

**Bước 1**: Tạo dự án console

```bash
dotnet new console -n LoggingDemo
cd LoggingDemo
```

**Bước 2**: Thêm package cần thiết

```bash
dotnet add package Microsoft.Extensions.Logging
dotnet add package Microsoft.Extensions.Logging.Console
```

**Bước 3**: Cập nhật code trong `Program.cs`

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

class Program
{
    static void Main(string[] args)
    {
        // Cấu hình DI
        var serviceProvider = new ServiceCollection()
            .AddLogging(builder => builder.AddConsole())
            .BuildServiceProvider();

        // Lấy logger
        var logger = serviceProvider.GetService<ILogger<Program>>();

        // Ghi log
        logger.LogInformation("Ứng dụng đã khởi động.");
        logger.LogWarning("Đây là một cảnh báo.");
        logger.LogError("Đây là một lỗi.");

        try
        {
            throw new Exception("Lỗi thử nghiệm!");
        }
        catch (Exception ex)
        {
            logger.LogCritical(ex, "Lỗi nghiêm trọng xảy ra!");
        }
    }
}
```

**Kết quả chạy**:

- Log sẽ được hiển thị trên console với định dạng:

```
info: Program[0]
      Ứng dụng đã khởi động.
warn: Program[0]
      Đây là một cảnh báo.
error: Program[0]
      Đây là một lỗi.
crit: Program[0]
      Lỗi nghiêm trọng xảy ra!
```

#### Ví dụ 2: Logging trong ASP.NET Core

Mục tiêu: Tích hợp logging vào một ứng dụng web ASP.NET Core.

**Bước 1**: Tạo dự án web

```bash
dotnet new webapi -n WebApiLoggingDemo
cd WebApiLoggingDemo
```

**Bước 2**: Cấu hình logging trong `appsettings.json`

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

**Bước 3**: Inject ILogger vào Controller
Tạo một controller mới `LogController.cs`:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

namespace WebApiLoggingDemo.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class LogController : ControllerBase
    {
        private readonly ILogger<LogController> _logger;

        public LogController(ILogger<LogController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public IActionResult Get()
        {
            _logger.LogInformation("Yêu cầu GET đã được thực hiện.");
            try
            {
                throw new Exception("Lỗi thử nghiệm trong API!");
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Lỗi xảy ra trong API.");
                return StatusCode(500, "Có lỗi xảy ra!");
            }
        }
    }
}
```

**Kết quả chạy**:

- Khi gọi API `/api/log`, log sẽ được ghi ra console:

```
info: WebApiLoggingDemo.Controllers.LogController[0]
      Yêu cầu GET đã được thực hiện.
error: WebApiLoggingDemo.Controllers.LogController[0]
      Lỗi xảy ra trong API.
```

#### Ví dụ 3: Sử dụng Serilog để ghi log vào file

Mục tiêu: Tích hợp Serilog để ghi log vào file thay vì console.

**Bước 1**: Thêm package Serilog

```bash
dotnet add package Serilog.AspNetCore
dotnet add package Serilog.Sinks.File
```

**Bước 2**: Cấu hình Serilog trong `Program.cs`

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;
using Serilog;

namespace WebApiLoggingDemo
{
    public class Program
    {
        public static void Main(string[] args)
        {
            // Cấu hình Serilog
            Log.Logger = new LoggerConfiguration()
                .WriteTo.File("logs/log-.txt", rollingInterval: RollingInterval.Day)
                .CreateLogger();

            try
            {
                Log.Information("Ứng dụng đang khởi động...");
                CreateHostBuilder(args).Build().Run();
            }
            catch (Exception ex)
            {
                Log.Fatal(ex, "Ứng dụng khởi động thất bại!");
            }
            finally
            {
                Log.CloseAndFlush();
            }
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .UseSerilog() // Sử dụng Serilog thay cho logger mặc định
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
    }
}
```

**Kết quả chạy**:

- Log sẽ được ghi vào file `logs/log-[ngày].txt`, ví dụ:

```
2025-07-09 14:20:35.123 +07:00 [INF] Ứng dụng đang khởi động...
2025-07-09 14:20:40.456 +07:00 [INF] Yêu cầu GET đã được thực hiện.
2025-07-09 14:20:40.789 +07:00 [ERR] Lỗi xảy ra trong API.
```

### 5. Một số lưu ý khi sử dụng Logging

- **Hiệu suất**: Tránh ghi quá nhiều log ở mức Trace/Debug trong môi trường production.
- **Cấu hình LogLevel**: Sử dụng `appsettings.json` để dễ dàng thay đổi mức độ log mà không cần sửa code.
- **Bảo mật**: Không ghi thông tin nhạy cảm (như mật khẩu, token) vào log.
- **Sử dụng Structured Logging**: Với Serilog, có thể ghi log dạng JSON để dễ dàng phân tích bằng các công cụ như ELK Stack.

### 6. Bài tập thực hành

1. Tạo một ứng dụng console sử dụng `Microsoft.Extensions.Logging` để ghi log ra console và file đồng thời.
2. Tích hợp Serilog vào một ứng dụng ASP.NET Core, ghi log vào cả file và database (sử dụng Serilog.Sinks.MSSqlServer).
3. Thử cấu hình LogLevel khác nhau trong `appsettings.json` và quan sát sự thay đổi trong output log.

## Tài liệu tham khảo

- [Microsoft.Extensions.Logging](https://docs.microsoft.com/en-us/dotnet/core/extensions/logging)
- [Serilog Documentation](https://serilog.net/)
- Video: [Học lập trình .NET - Phần 24 - Logging](https://www.youtube.com/watch?v=V4c40a4crQM)
