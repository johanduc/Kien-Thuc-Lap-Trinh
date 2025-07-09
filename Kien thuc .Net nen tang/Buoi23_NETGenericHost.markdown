# Buổi 23: .NET Generic Host

## Mục tiêu

- Hiểu khái niệm .NET Generic Host
- Tìm hiểu cách sử dụng Generic Host trong ứng dụng Console
- Thực hành xây dựng ứng dụng với Generic Host
- Tìm hiểu về Dependency Injection (DI) và Hosted Service trong Generic Host

## Nội dung chính

### 1. Giới thiệu .NET Generic Host

- **Generic Host** là một thành phần trong .NET (từ .NET Core 2.1 trở lên) dùng để xây dựng các ứng dụng không phải web, ví dụ như:
  - Console applications
  - Windows Services
  - Linux Daemons
  - Background workers
- Generic Host cung cấp các tính năng:

  - **Dependency Injection (DI)**: Quản lý các dịch vụ thông qua DI container.
  - **Hosted Services**: Quản lý các tác vụ chạy nền (background tasks).
  - **Logging**: Cung cấp hệ thống logging tích hợp.
  - **Configuration**: Hỗ trợ cấu hình ứng dụng thông qua appsettings.json, environment variables, v.v.
  - **Lifetime Management**: Quản lý vòng đời của ứng dụng (khởi động, chạy, dừng).

- So sánh với **Web Host** (dùng cho ứng dụng ASP.NET Core):
  - Web Host chuyên dụng cho ứng dụng web, tích hợp middleware, HTTP pipeline.
  - Generic Host linh hoạt hơn, dùng cho các ứng dụng không cần HTTP server.

### 2. Cấu trúc cơ bản của Generic Host

- **HostBuilder**: Lớp chính để cấu hình và xây dựng host.
- **IHost**: Đối tượng đại diện cho host, quản lý vòng đời và các dịch vụ.
- **Hosted Service**: Các dịch vụ chạy nền, triển khai interface `IHostedService`.

### 3. Các bước xây dựng ứng dụng với Generic Host

1. **Tạo dự án Console**:
   ```bash
   dotnet new console -n GenericHostDemo
   cd GenericHostDemo
   ```
2. **Thêm package cần thiết**:
   - Cài đặt package `Microsoft.Extensions.Hosting`:
     ```bash
     dotnet add package Microsoft.Extensions.Hosting
     ```
3. **Cấu hình Host**:

   - Trong file `Program.cs`, sử dụng `HostBuilder` để cấu hình host:

     ```csharp
     using Microsoft.Extensions.Hosting;
     using Microsoft.Extensions.DependencyInjection;
     using System.Threading.Tasks;

     class Program
     {
         static async Task Main(string[] args)
         {
             await Host.CreateDefaultBuilder(args)
                 .ConfigureServices((context, services) =>
                 {
                     // Cấu hình dịch vụ
                     services.AddHostedService<MyBackgroundService>();
                 })
                 .Build()
                 .RunAsync();
         }
     }
     ```

4. **Tạo Hosted Service**:

   - Tạo một lớp triển khai `IHostedService` để xử lý logic chạy nền:

     ```csharp
     using Microsoft.Extensions.Hosting;
     using System;
     using System.Threading;
     using System.Threading.Tasks;

     public class MyBackgroundService : IHostedService
     {
         public Task StartAsync(CancellationToken cancellationToken)
         {
             Console.WriteLine("Background service is starting...");
             return Task.CompletedTask;
         }

         public Task StopAsync(CancellationToken cancellationToken)
         {
             Console.WriteLine("Background service is stopping...");
             return Task.CompletedTask;
         }
     }
     ```

5. **Chạy ứng dụng**:
   - Chạy lệnh:
     ```bash
     dotnet run
     ```
   - Ứng dụng sẽ khởi động và hiển thị thông báo từ `MyBackgroundService`.

### 4. Dependency Injection trong Generic Host

- **DI** được tích hợp sẵn trong Generic Host.
- Ví dụ: Đăng ký và sử dụng một dịch vụ:

  ```csharp
  public interface IMyService
  {
      void DoWork();
  }

  public class MyService : IMyService
  {
      public void DoWork()
      {
          Console.WriteLine("Doing work in MyService!");
      }
  }

  // Trong Program.cs
  services.AddSingleton<IMyService, MyService>();

  // Sử dụng trong MyBackgroundService
  public class MyBackgroundService : IHostedService
  {
      private readonly IMyService _myService;

      public MyBackgroundService(IMyService myService)
      {
          _myService = myService;
      }

      public Task StartAsync(CancellationToken cancellationToken)
      {
          _myService.DoWork();
          return Task.CompletedTask;
      }

      public Task StopAsync(CancellationToken cancellationToken)
      {
          return Task.CompletedTask;
      }
  }
  ```

### 5. Sử dụng Logging

- Generic Host tích hợp sẵn logging. Để sử dụng:

  ```csharp
  public class MyBackgroundService : IHostedService
  {
      private readonly ILogger<MyBackgroundService> _logger;

      public MyBackgroundService(ILogger<MyBackgroundService> logger)
      {
          _logger = logger;
      }

      public Task StartAsync(CancellationToken cancellationToken)
      {
          _logger.LogInformation("Background service is starting...");
          return Task.CompletedTask;
      }

      public Task StopAsync(CancellationToken cancellationToken)
      {
          _logger.LogInformation("Background service is stopping...");
          return Task.CompletedTask;
      }
  }
  ```

- Logging mặc định ghi vào console, có thể cấu hình thêm để ghi vào file, Azure, hoặc các hệ thống khác.

### 6. Sử dụng Configuration

- Generic Host hỗ trợ đọc cấu hình từ nhiều nguồn (appsettings.json, environment variables, command line).
- Ví dụ: Thêm file `appsettings.json`:
  ```json
  {
    "MySettings": {
      "Message": "Hello from appsettings!"
    }
  }
  ```
- Đọc cấu hình trong ứng dụng:

  ```csharp
  public class MyBackgroundService : IHostedService
  {
      private readonly IConfiguration _configuration;

      public MyBackgroundService(IConfiguration configuration)
      {
          _configuration = configuration;
      }

      public Task StartAsync(CancellationToken cancellationToken)
      {
          string message = _configuration["MySettings:Message"];
          Console.WriteLine(message);
          return Task.CompletedTask;
      }

      public Task StopAsync(CancellationToken cancellationToken)
      {
          return Task.CompletedTask;
      }
  }
  ```

### 7. Bài tập ví dụ

#### Bài tập 1: Tạo ứng dụng đếm ngược

- Yêu cầu:

  - Tạo một `HostedService` chạy một tác vụ đếm ngược từ 10 về 0, mỗi giây in ra một số.
  - Sử dụng `ILogger` để ghi log.
  - Dừng tác vụ khi đếm về 0 hoặc khi ứng dụng tắt.

- Giải pháp:

  ```csharp
  using Microsoft.Extensions.Hosting;
  using Microsoft.Extensions.Logging;
  using System;
  using System.Threading;
  using System.Threading.Tasks;

  public class CountdownService : IHostedService, IDisposable
  {
      private readonly ILogger<CountdownService> _logger;
      private Timer _timer;

      public CountdownService(ILogger<CountdownService> logger)
      {
          _logger = logger;
      }

      public Task StartAsync(CancellationToken cancellationToken)
      {
          _logger.LogInformation("Countdown service is starting...");
          int count = 10;
          _timer = new Timer(DoWork, count, TimeSpan.Zero, TimeSpan.FromSeconds(1));
          return Task.CompletedTask;
      }

      private void DoWork(object state)
      {
          int count = (int)state;
          if (count > 0)
          {
              _logger.LogInformation($"Counting down: {count}");
              Interlocked.Decrement(ref count);
          }
          else
          {
              _logger.LogInformation("Countdown finished!");
              _timer?.Change(Timeout.Infinite, 0);
          }
      }

      public Task StopAsync(CancellationToken cancellationToken)
      {
          _logger.LogInformation("Countdown service is stopping...");
          _timer?.Change(Timeout.Infinite, 0);
          return Task.CompletedTask;
      }

      public void Dispose()
      {
          _timer?.Dispose();
      }
  }
  ```

- Cấu hình trong `Program.cs`:
  ```csharp
  await Host.CreateDefaultBuilder(args)
      .ConfigureServices((context, services) =>
      {
          services.AddHostedService<CountdownService>();
      })
      .Build()
      .RunAsync();
  ```

#### Bài tập 2: Tích hợp cấu hình và DI

- Yêu cầu:
  - Tạo một dịch vụ đọc thông điệp từ `appsettings.json`.
  - Sử dụng DI để inject dịch vụ vào `HostedService`.
- Giải pháp:
  - File `appsettings.json`:
    ```json
    {
        "MessageSettings":
    ```

{
"Greeting": "Welcome to Generic Host!"
}
}

````

- Dịch vụ:

  ```csharp
  public interface IMessageService
  {
      string GetGreeting();
  }

  public class MessageService : IMessageService
  {
      private readonly IConfiguration _configuration;

      public MessageService(IConfiguration configuration)
      {
          _configuration = configuration;
      }

      public string GetGreeting()
      {
          return _configuration["MessageSettings:Greeting"];
      }
  }
````

- Hosted Service:

  ```csharp
  public class GreetingService : IHostedService
  {
      private readonly IMessageService _messageService;
      private readonly ILogger<GreetingService> _logger;

      public GreetingService(IMessageService messageService, ILogger<GreetingService> logger)
      {
          _messageService = messageService;
          _logger = logger;
      }

      public Task StartAsync(CancellationToken cancellationToken)
      {
          _logger.LogInformation("Greeting service is starting...");
          _logger.LogInformation(_messageService.GetGreeting());
          return Task.CompletedTask;
      }

      public Task StopAsync(CancellationToken cancellationToken)
      {
          _logger.LogInformation("Greeting service is stopping...");
          return Task.CompletedTask;
      }
  }
  ```

- Cấu hình trong `Program.cs`:
  ```csharp
  await Host.CreateDefaultBuilder(args)
      .ConfigureServices((context, services) =>
      {
          services.AddSingleton<IMessageService, MessageService>();
          services.AddHostedService<GreetingService>();
      })
      .Build()
      .RunAsync();
  ```

### 8. Một số lưu ý

- **Vòng đời của Host**:
  - `StartAsync`: Chạy khi ứng dụng khởi động.
  - `StopAsync`: Chạy khi ứng dụng dừng (Ctrl+C hoặc SIGTERM).
- **Background tasks**:

  - Sử dụng `BackgroundService` thay vì `IHostedService` nếu cần thực hiện tác vụ lặp lại hoặc phức tạp hơn.
  - Ví dụ:

    ```csharp
    public class MyBackgroundService : BackgroundService
    {
        private readonly ILogger<MyBackgroundService> _logger;

        public MyBackgroundService(ILogger<MyBackgroundService> logger)
        {
            _logger = logger;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Background task is running...");
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
    ```

- **Cấu hình nâng cao**:
  - Có thể thêm các provider logging (Serilog, NLog).
  - Tích hợp với các dịch vụ như Azure, AWS, hoặc các hệ thống message queue (RabbitMQ, Kafka).

### 9. Tài liệu tham khảo

- Microsoft Docs: [Generic Host in .NET](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/generic-host)
- Microsoft Docs: [Dependency Injection in .NET](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)
- Microsoft Docs: [Logging in .NET](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging)
