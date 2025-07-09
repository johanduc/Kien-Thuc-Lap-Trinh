# Buổi 28: Caching và Decorator Design Pattern

## Mục tiêu

- Hiểu khái niệm **Caching** trong lập trình và vai trò của nó trong việc cải thiện hiệu năng ứng dụng.
- Nắm vững cách triển khai **Caching** trong .NET.
- Tìm hiểu về **Decorator Design Pattern** và cách sử dụng nó để mở rộng chức năng của các lớp một cách linh hoạt.
- Áp dụng kiến thức thông qua các ví dụ và bài tập thực hành.

## Nội dung chính

### 1. Caching là gì?

- **Caching** là kỹ thuật lưu trữ dữ liệu tạm thời để giảm thời gian truy cập hoặc xử lý dữ liệu, từ đó cải thiện hiệu năng ứng dụng.
- Các loại caching phổ biến:
  - **In-Memory Caching**: Lưu trữ dữ liệu trong bộ nhớ RAM (nhanh, nhưng mất dữ liệu khi ứng dụng khởi động lại).
  - **Distributed Caching**: Lưu trữ dữ liệu trên một hệ thống phân tán (như Redis, Memcached).
  - **Output Caching**: Lưu trữ kết quả đầu ra của một request (thường dùng trong ứng dụng web).
- Trong .NET, **IMemoryCache** là một dịch vụ tích hợp sẵn để hỗ trợ In-Memory Caching.

### 2. Sử dụng IMemoryCache trong .NET

- **IMemoryCache** là một dịch vụ caching có sẵn trong .NET, được cung cấp bởi gói `Microsoft.Extensions.Caching.Memory`.
- Các tính năng chính:
  - Lưu trữ dữ liệu dạng key-value.
  - Hỗ trợ thời gian sống (TTL - Time To Live) và cơ chế trượt (Sliding Expiration).
  - Dễ tích hợp vào ứng dụng ASP.NET Core.

#### Cách sử dụng IMemoryCache

1. **Cấu hình dịch vụ**:

   - Thêm dịch vụ `IMemoryCache` vào container DI (Dependency Injection) trong `Program.cs`:
     ```csharp
     builder.Services.AddMemoryCache();
     ```

2. **Tiêm IMemoryCache vào lớp hoặc controller**:

   - Ví dụ trong một controller:
     ```csharp
     private readonly IMemoryCache _cache;
     public MyController(IMemoryCache cache)
     {
         _cache = cache;
     }
     ```

3. **Thêm và truy xuất dữ liệu từ cache**:

   - Lưu dữ liệu vào cache:
     ```csharp
     _cache.Set("key", "value", TimeSpan.FromMinutes(5)); // Lưu với TTL 5 phút
     ```
   - Lấy dữ liệu từ cache:
     ```csharp
     if (_cache.TryGetValue("key", out string value))
     {
         // Sử dụng giá trị từ cache
         Console.WriteLine($"Cache hit: {value}");
     }
     else
     {
         // Xử lý khi không tìm thấy trong cache
         Console.WriteLine("Cache miss");
     }
     ```

4. **Cơ chế trượt (Sliding Expiration)**:
   - Dữ liệu sẽ được làm mới thời gian sống mỗi khi được truy cập:
     ```csharp
     var cacheEntryOptions = new MemoryCacheEntryOptions
     {
         SlidingExpiration = TimeSpan.FromMinutes(2)
     };
     _cache.Set("key", "value", cacheEntryOptions);
     ```

### 3. Decorator Design Pattern

- **Decorator Pattern** là một mẫu thiết kế cấu trúc, cho phép mở rộng chức năng của một đối tượng mà không cần thay đổi mã nguồn của lớp gốc.
- Ý tưởng chính: Bọc một đối tượng bằng một lớp khác để thêm hoặc sửa đổi hành vi.
- Ứng dụng:
  - Thêm chức năng caching vào một dịch vụ.
  - Mở rộng các phương thức mà không sửa đổi lớp gốc.
- Cấu trúc:
  - **Component**: Giao diện hoặc lớp trừu tượng định nghĩa các phương thức.
  - **Concrete Component**: Lớp cụ thể triển khai Component.
  - **Decorator**: Lớp trừu tượng bọc Component, triển khai cùng giao diện.
  - **Concrete Decorator**: Lớp cụ thể thêm chức năng vào Component.

#### Ví dụ áp dụng Decorator Pattern với Caching

- Giả sử có một dịch vụ lấy dữ liệu người dùng:

  ```csharp
  public interface IUserService
  {
      string GetUserData(int userId);
  }

  public class UserService : IUserService
  {
      public string GetUserData(int userId)
      {
          // Giả lập gọi database
          return $"User data for ID {userId}";
      }
  }
  ```

- Tạo Decorator để thêm caching:

  ```csharp
  public class CachedUserService : IUserService
  {
      private readonly IUserService _userService;
      private readonly IMemoryCache _cache;

      public CachedUserService(IUserService userService, IMemoryCache cache)
      {
          _userService = userService;
          _cache = cache;
      }

      public string GetUserData(int userId)
      {
          string cacheKey = $"User_{userId}";
          if (_cache.TryGetValue(cacheKey, out string cachedData))
          {
              return cachedData;
          }

          var data = _userService.GetUserData(userId);
          _cache.Set(cacheKey, data, TimeSpan.FromMinutes(5));
          return data;
      }
  }
  ```

- Cấu hình DI:
  ```csharp
  builder.Services.AddMemoryCache();
  builder.Services.AddSingleton<IUserService, UserService>();
  builder.Services.AddSingleton<IUserService>(provider =>
      new CachedUserService(
          provider.GetService<IUserService>(),
          provider.GetService<IMemoryCache>()
      ));
  ```

### 4. Bài tập ví dụ

#### Bài tập 1: Tạo cache cho danh sách sản phẩm

- **Yêu cầu**: Viết một dịch vụ lấy danh sách sản phẩm từ "database" (giả lập) và sử dụng IMemoryCache để lưu trữ tạm thời kết quả trong 10 phút.
- **Giải pháp**:

  ```csharp
  public interface IProductService
  {
      List<string> GetProducts();
  }

  public class ProductService : IProductService
  {
      public List<string> GetProducts()
      {
          // Giả lập truy vấn database
          return new List<string> { "Product1", "Product2", "Product3" };
      }
  }

  public plan CachedProductService : IProductService
  {
      private readonly IProductService _productService;
      private readonly IMemoryCache _cache;

      public CachedProductService(IProductService productService, IMemoryCache cache)
      {
          _productService = productService;
          _cache = cache;
      }

      public List<string> GetProducts()
      {
          string cacheKey = "ProductList";
          if (_cache.TryGetValue(cacheKey, out List<string> cachedProducts))
          {
              return cachedProducts;
          }

          var products = _productService.GetProducts();
          var cacheEntryOptions = new MemoryCacheEntryOptions
          {
              AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10)
          };
          _cache.Set(cacheKey, products, cacheEntryOptions);
          return products;
      }
  }
  ```

- **Cấu hình DI**:
  ```csharp
  builder.Services.AddMemoryCache();
  builder.Services.AddSingleton<IProductService, ProductService>();
  builder.Services.AddSingleton<IProductService>(provider =>
      new CachedProductService(
          provider.GetService<IProductService>(),
          provider.GetService<IMemoryCache>()
      ));
  ```

#### Bài tập 2: Kết hợp Decorator Pattern với Logging

- **Yêu cầu**: Tạo một Decorator để ghi log thời gian thực thi của phương thức `GetUserData`.
- **Giải pháp**:

  ```csharp
  public class LoggingUserService : IUserService
  {
      private readonly IUserService _userService;
      private readonly ILogger<LoggingUserService> _logger;

      public LoggingUserService(IUserService userService, ILogger<LoggingUserService> logger)
      {
          _userService = userService;
          _logger = logger;
      }

      public string GetUserData(int userId)
      {
          var stopwatch = Stopwatch.StartNew();
          var result = _userService.GetUserData(userId);
          stopwatch.Stop();
          _logger.LogInformation($"GetUserData({userId}) took {stopwatch.ElapsedMilliseconds}ms");
          return result;
      }
  }
  ```

- **Cấu hình DI**:
  ```csharp
  builder.Services.AddMemoryCache();
  builder.Services.AddLogging();
  builder.Services.AddSingleton<IUserService, UserService>();
  builder.Services.AddSingleton<IUserService>(provider =>
      new LoggingUserService(
          new CachedUserService(
              provider.GetService<IUserService>(),
              provider.GetService<IMemoryCache>()
          ),
          provider.GetService<ILogger<LoggingUserService>>()
      ));
  ```

### 5. Lưu ý khi sử dụng Caching

- **Khi nào nên dùng cache**:
  - Dữ liệu ít thay đổi hoặc tốn nhiều tài nguyên để truy xuất.
  - Cải thiện hiệu năng cho các thao tác lặp lại.
- **Rủi ro**:
  - Dữ liệu trong cache có thể không đồng bộ với nguồn (stale data).
  - Sử dụng quá nhiều bộ nhớ nếu không quản lý tốt.
- **Giải pháp**:
  - Sử dụng thời gian sống hợp lý (TTL).
  - Xóa cache khi dữ liệu gốc thay đổi (cache invalidation).
  - Sử dụng Distributed Cache (như Redis) cho ứng dụng lớn.

### 6. Kết luận

- **Caching** giúp tối ưu hóa hiệu năng ứng dụng bằng cách giảm tải cho hệ thống.
- **Decorator Pattern** là một cách tiếp cận linh hoạt để mở rộng chức năng mà không sửa đổi mã nguồn gốc.
- Kết hợp cả hai kỹ thuật giúp xây dựng ứng dụng mạnh mẽ và dễ bảo trì.
