# Buổi 39: Giải bài tập viết Web server (Phần 3)

## Mục tiêu
- Tiếp tục phát triển Web server trong .NET từ các phần trước.
- Tìm hiểu cách xử lý các HTTP request phức tạp hơn (GET, POST).
- Tích hợp routing cơ bản để xử lý các endpoint khác nhau.
- Xử lý response với các định dạng như HTML, JSON.
- Cải thiện cấu trúc code và tối ưu hóa hiệu suất.

## Nội dung chính

### 1. Ôn tập kiến thức từ phần trước
- **Web server cơ bản**: Ở các phần trước (phần 1 và 2), chúng ta đã xây dựng một Web server đơn giản sử dụng `TcpListener` để lắng nghe các kết nối từ client và trả về response cơ bản (ví dụ: một trang HTML tĩnh).
- **HTTP Request/Response**: Hiểu cấu trúc của một HTTP request (method, path, headers) và cách trả về response với status code (200 OK, 404 Not Found, v.v.).
- **Mục tiêu phần 3**: Mở rộng chức năng của Web server để hỗ trợ routing, xử lý nhiều loại request, và trả về các định dạng dữ liệu khác nhau.

### 2. Xử lý HTTP Request nâng cao
- **Phân tích HTTP Request**: Phân tích chuỗi request để lấy thông tin về method (GET, POST), path, và các query parameters.
- **Routing cơ bản**: Xây dựng một cơ chế để ánh xạ các URL (endpoint) đến các handler tương ứng.
  - Ví dụ: `/home` trả về trang chủ, `/api/users` trả về danh sách người dùng dưới dạng JSON.
- **Xử lý POST request**: Đọc dữ liệu từ body của request (ví dụ: dữ liệu form hoặc JSON) và xử lý.

#### Ví dụ: Phân tích HTTP Request
```csharp
private static void HandleClient(TcpClient client)
{
    using (var stream = client.GetStream())
    using (var reader = new StreamReader(stream))
    {
        // Đọc request
        string request = "";
        while (true)
        {
            string line = reader.ReadLine();
            if (string.IsNullOrEmpty(line)) break;
            request += line + "\r\n";
        }

        // Phân tích request
        string[] lines = request.Split(new[] { "\r\n" }, StringSplitOptions.None);
        string[] firstLine = lines[0].Split(' ');
        string method = firstLine[0]; // GET, POST, v.v.
        string path = firstLine[1];  // /home, /api/users, v.v.

        Console.WriteLine($"Method: {method}, Path: {path}");
    }
}
```

### 3. Xây dựng hệ thống Routing
- **Ý tưởng**: Tạo một danh sách các route (đường dẫn) và ánh xạ chúng đến các hàm xử lý (handler).
- **Cấu trúc Route**:
  - Một route bao gồm: HTTP method (GET, POST), path (URL), và handler (hàm xử lý).
  - Ví dụ: `(GET, "/home", HomeHandler)`.

#### Ví dụ: Cấu trúc Route
```csharp
public class Route
{
    public string Method { get; set; }
    public string Path { get; set; }
    public Action<HttpContext> Handler { get; set; }
}

public class HttpContext
{
    public string Method { get; set; }
    public string Path { get; set; }
    public string Response { get; set; }
}
```

#### Ví dụ: Định nghĩa Route và xử lý
```csharp
private static List<Route> routes = new List<Route>
{
    new Route { Method = "GET", Path = "/home", Handler = HomeHandler },
    new Route { Method = "GET", Path = "/api/users", Handler = UsersHandler }
};

private static void HomeHandler(HttpContext context)
{
    context.Response = "<html><body><h1>Welcome to Home Page</h1></body></html>";
}

private static void UsersHandler(HttpContext context)
{
    context.Response = "{\"users\": [{\"id\": 1, \"name\": \"Alice\"}, {\"id\": 2, \"name\": \"Bob\"}]}";
}
```

### 4. Trả về Response với các định dạng khác nhau
- **HTML Response**: Trả về nội dung HTML để hiển thị giao diện.
- **JSON Response**: Trả về dữ liệu dạng JSON cho các API endpoint.
- **Status Code**: Sử dụng các mã trạng thái HTTP (200, 404, 500, v.v.) để thông báo kết quả.

#### Ví dụ: Gửi Response
```csharp
private static void SendResponse(NetworkStream stream, string content, string contentType, int statusCode = 200)
{
    string response = $"HTTP/1.1 {statusCode} OK\r\n" +
                     $"Content-Type: {contentType}; charset=UTF-8\r\n" +
                     $"Content-Length: {content.Length}\r\n" +
                     $"\r\n" +
                     content;

    byte[] responseBytes = Encoding.UTF8.GetBytes(response);
    stream.Write(responseBytes, 0, responseBytes.Length);
}
```

### 5. Bài tập ví dụ
#### Bài tập 1: Xây dựng endpoint trả về danh sách sản phẩm
- Yêu cầu: Tạo một endpoint `/api/products` (method GET) trả về danh sách sản phẩm dưới dạng JSON.
- Dữ liệu mẫu:
  ```json
  [
      {"id": 1, "name": "Laptop", "price": 1000},
      {"id": 2, "name": "Phone", "price": 500}
  ]
  ```

#### Giải pháp
```csharp
private static void ProductsHandler(HttpContext context)
{
    string json = "[{\"id\": 1, \"name\": \"Laptop\", \"price\": 1000}, {\"id\": 2, \"name\": \"Phone\", \"price\": 500}]";
    context.Response = json;
}
```

#### Bài tập 2: Xử lý POST request để thêm sản phẩm
- Yêu cầu: Tạo endpoint `/api/products` (method POST) để nhận dữ liệu sản phẩm từ client và lưu vào danh sách.
- Dữ liệu gửi từ client (body):
  ```json
  {"name": "Tablet", "price": 300}
  ```

#### Giải pháp
```csharp
private static List<Product> products = new List<Product>
{
    new Product { Id = 1, Name = "Laptop", Price = 1000 },
    new Product { Id = 2, Name = "Phone", Price = 500 }
};

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public double Price { get; set; }
}

private static void AddProductHandler(HttpContext context)
{
    // Giả lập đọc body từ request
    string body = "{\"name\": \"Tablet\", \"price\": 300}"; // Trong thực tế, đọc từ stream
    var product = JsonSerializer.Deserialize<Product>(body);
    products.Add(product);
    context.Response = "{\"message\": \"Product added successfully\"}";
}
```

### 6. Tối ưu hóa và cải thiện
- **Tách biệt logic**: Tách code xử lý request, routing, và response thành các lớp riêng biệt để dễ bảo trì.
- **Xử lý lỗi**: Thêm cơ chế xử lý lỗi (try-catch) để trả về response 500 khi có lỗi.
- **Hiệu suất**: Sử dụng `async/await` để xử lý các kết nối không đồng bộ, cải thiện khả năng xử lý nhiều client cùng lúc.

#### Ví dụ: Sử dụng async/await
```csharp
private static async Task HandleClientAsync(TcpClient client)
{
    using (var stream = client.GetStream())
    using (var reader = new StreamReader(stream))
    {
        string request = await reader.ReadToEndAsync();
        // Xử lý request và gửi response
        await SendResponseAsync(stream, "<h1>Hello, World!</h1>", "text/html");
    }
}

private static async Task SendResponseAsync(NetworkStream stream, string content, string contentType, int statusCode = 200)
{
    string response = $"HTTP/1.1 {statusCode} OK\r\n" +
                     $"Content-Type: {contentType}; charset=UTF-8\r\n" +
                     $"Content-Length: {content.Length}\r\n" +
                     $"\r\n" +
                     content;

    byte[] responseBytes = Encoding.UTF8.GetBytes(response);
    await stream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

## Bài tập thực hành
1. **Thêm endpoint `/api/orders`**: Tạo endpoint trả về danh sách đơn hàng dưới dạng JSON.
2. **Xử lý query parameters**: Cập nhật endpoint `/api/products` để hỗ trợ lọc sản phẩm theo giá (ví dụ: `/api/products?maxPrice=1000`).
3. **Xử lý lỗi 404**: Thêm logic để trả về response 404 nếu client truy cập vào một endpoint không tồn tại.
4. **Tích hợp middleware**: Tạo một middleware để ghi log mỗi request (method, path, thời gian).

## Tài liệu tham khảo
- [Học lập trình .NET - Phần 39 - Giải bài tập viết Web server (phần 3)](https://www.youtube.com/watch?v=QG9F0HUXnDE)
- Tài liệu về `TcpListener` và `HttpListener` trong .NET.
- Hướng dẫn về JSON serialization trong .NET.