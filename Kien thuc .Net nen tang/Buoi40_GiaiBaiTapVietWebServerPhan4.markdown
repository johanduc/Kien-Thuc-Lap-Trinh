# Buổi 40 - Giải bài tập viết Web server (phần 4)

## Mục tiêu
- Tiếp tục phát triển Web server trong .NET.
- Xử lý các request HTTP từ client, bao gồm việc trả về nội dung động (dynamic content) dựa trên các tham số trong URL.
- Hiểu cách sử dụng `StreamWriter` và `StreamReader` để đọc/ghi dữ liệu trong giao tiếp mạng.
- Xây dựng các phương thức xử lý yêu cầu (request) và phản hồi (response) HTTP.

## Nội dung chính

### 1. Ôn tập các phần trước
- Trong các phần trước (phần 1, 2, 3), chúng ta đã:
  - Tạo một Web server cơ bản sử dụng `TcpListener` để lắng nghe kết nối từ client.
  - Xử lý các request HTTP cơ bản (GET) và trả về nội dung tĩnh (static content) như file HTML.
  - Phân tích cú pháp (parse) URL để lấy thông tin về đường dẫn (path) và tham số (query string).
  - Tạo phản hồi HTTP với các header cơ bản như `Content-Type`, `Content-Length`.

- Phần 4 này tập trung vào:
  - Xử lý các tham số trong query string để tạo nội dung động.
  - Tùy chỉnh phản hồi dựa trên yêu cầu của client.

### 2. Xử lý query string và nội dung động
- **Query string** là phần trong URL sau dấu `?`, chứa các cặp key-value (ví dụ: `?name=John&age=25`).
- Mục tiêu: Lấy các tham số từ query string và sử dụng chúng để tạo nội dung HTML động.

#### Ví dụ: Xử lý query string
- Giả sử client gửi yêu cầu: `http://localhost:8080/greeting?name=John`.
- Web server sẽ trả về một trang HTML hiển thị lời chào: "Hello, John!".

### 3. Cấu trúc mã nguồn
- Tiếp tục sử dụng dự án Web server từ các phần trước.
- Cấu trúc cơ bản của Web server:
  ```csharp
  using System;
  using System.IO;
  using System.Net;
  using System.Net.Sockets;
  using System.Text;

  class Program
  {
      static void Main(string[] args)
      {
          TcpListener server = new TcpListener(IPAddress.Any, 8080);
          server.Start();
          Console.WriteLine("Server started on port 8080...");

          while (true)
          {
              TcpClient client = server.AcceptTcpClient();
              ProcessClient(client);
          }
      }

      static void ProcessClient(TcpClient client)
      {
          // Xử lý kết nối client
      }
  }
  ```

### 4. Phân tích và xử lý request
- Đọc dữ liệu từ client:
  - Sử dụng `NetworkStream` để đọc yêu cầu HTTP từ client.
  - Phân tích dòng đầu tiên của request để lấy phương thức (method), đường dẫn (path), và query string.
- Ví dụ request từ client:
  ```
  GET /greeting?name=John HTTP/1.1
  Host: localhost:8080
  ```

#### Code xử lý request
```csharp
static void ProcessClient(TcpClient client)
{
    NetworkStream stream = client.GetStream();
    StreamReader reader = new StreamReader(stream);
    StreamWriter writer = new StreamWriter(stream) { AutoFlush = true };

    // Đọc dòng đầu tiên của request
    string requestLine = reader.ReadLine();
    if (string.IsNullOrEmpty(requestLine)) return;

    // Phân tích request
    string[] tokens = requestLine.Split(' ');
    string method = tokens[0];
    string url = tokens[1];

    // Tách path và query string
    string path = url.Contains("?") ? url.Substring(0, url.IndexOf("?")) : url;
    string queryString = url.Contains("?") ? url.Substring(url.IndexOf("?") + 1) : "";

    // Đọc các header khác (nếu cần)
    while (!string.IsNullOrEmpty(reader.ReadLine())) { }

    // Xử lý request và gửi response
    if (method == "GET" && path == "/greeting")
    {
        HandleGreetingRequest(writer, queryString);
    }
    else
    {
        SendNotFoundResponse(writer);
    }

    client.Close();
}
```

### 5. Xử lý query string và tạo nội dung động
- Hàm `HandleGreetingRequest` sẽ phân tích query string và tạo nội dung HTML dựa trên tham số `name`.

#### Code xử lý query string
```csharp
static void HandleGreetingRequest(StreamWriter writer, string queryString)
{
    // Mặc định tên là "Guest" nếu không có tham số name
    string name = "Guest";

    // Phân tích query string
    if (!string.IsNullOrEmpty(queryString))
    {
        var parameters = queryString.Split('&');
        foreach (var param in parameters)
        {
            var pair = param.Split('=');
            if (pair[0] == "name" && pair.Length > 1)
            {
                name = pair[1];
            }
        }
    }

    // Tạo nội dung HTML động
    string htmlContent = $@"<!DOCTYPE html>
<html>
<head><title>Greeting</title></head>
<body>
    <h1>Hello, {name}!</h1>
</body>
</html>";

    // Gửi response
    writer.WriteLine("HTTP/1.1 200 OK");
    writer.WriteLine("Content-Type: text/html");
    writer.WriteLine($"Content-Length: {Encoding.UTF8.GetByteCount(htmlContent)}");
    writer.WriteLine();
    writer.WriteLine(htmlContent);
}
```

### 6. Xử lý trường hợp không tìm thấy tài nguyên
- Nếu client gửi request không hợp lệ (ví dụ: đường dẫn không tồn tại), trả về mã lỗi 404.

#### Code xử lý 404
```csharp
static void SendNotFoundResponse(StreamWriter writer)
{
    string htmlContent = @"<!DOCTYPE html>
<html>
<head><title>404 Not Found</title></head>
<body>
    <h1>404 Not Found</h1>
    <p>The requested resource was not found on the server.</p>
</body>
</html>";

    writer.WriteLine("HTTP/1.1 404 Not Found");
    writer.WriteLine("Content-Type: text/html");
    writer.WriteLine($"Content-Length: {Encoding.UTF8.GetByteCount(htmlContent)}");
    writer.WriteLine();
    writer.WriteLine(htmlContent);
}
```

### 7. Bài tập ví dụ
- **Yêu cầu 1**: Mở rộng Web server để xử lý thêm một endpoint `/info?age=25&city=Hanoi`, trả về HTML hiển thị thông tin: "You are 25 years old, living in Hanoi."
- **Yêu cầu 2**: Thêm xử lý lỗi khi tham số không hợp lệ (ví dụ: age không phải số).
- **Giải pháp**:
  - Tương tự hàm `HandleGreetingRequest`, tạo hàm `HandleInfoRequest` để xử lý endpoint `/info`.
  - Kiểm tra tham số `age` xem có phải là số hay không bằng `int.TryParse`.

#### Code giải bài tập
```csharp
static void HandleInfoRequest(StreamWriter writer, string queryString)
{
    string age = "Unknown";
    string city = "Unknown";

    // Phân tích query string
    if (!string.IsNullOrEmpty(queryString))
    {
        var parameters = queryString.Split('&');
        foreach (var param in parameters)
        {
            var pair = param.Split('=');
            if (pair.Length > 1)
            {
                if (pair[0] == "age" && int.TryParse(pair[1], out int parsedAge))
                {
                    age = parsedAge.ToString();
                }
                else if (pair[0] == "city")
                {
                    city = pair[1];
                }
            }
        }
    }

    // Tạo nội dung HTML
    string htmlContent = $@"<!DOCTYPE html>
<html>
<head><title>Info</title></head>
<body>
    <h1>You are {age} years old, living in {city}.</h1>
</body>
</html>";

    // Gửi response
    writer.WriteLine("HTTP/1.1 200 OK");
    writer.WriteLine("Content-Type: text/html");
    writer.WriteLine($"Content-Length: {Encoding.UTF8.GetByteCount(htmlContent)}");
    writer.WriteLine();
    writer.WriteLine(htmlContent);
}
```

- Cập nhật hàm `ProcessClient` để xử lý endpoint `/info`:
```csharp
if (method == "GET" && path == "/greeting")
{
    HandleGreetingRequest(writer, queryString);
}
else if (method == "GET" && path == "/info")
{
    HandleInfoRequest(writer, queryString);
}
else
{
    SendNotFoundResponse(writer);
}
```

### 8. Lưu ý triển khai
- **Đóng client**: Đảm bảo đóng `TcpClient` sau khi xử lý xong để tránh rò rỉ tài nguyên.
- **Xử lý ngoại lệ**: Thêm try-catch trong hàm `ProcessClient` để xử lý các lỗi mạng hoặc định dạng request không hợp lệ.
- **Đa luồng**: Hiện tại server xử lý từng client tuần tự. Để hỗ trợ nhiều client đồng thời, cần sử dụng đa lu threading hoặc async/await.

#### Code xử lý ngoại lệ
```csharp
static void ProcessClient(TcpClient client)
{
    try
    {
        NetworkStream stream = client.GetStream();
        StreamReader reader = new StreamReader(stream);
        StreamWriter writer = new StreamWriter(stream) { AutoFlush = true };

        string requestLine = reader.ReadLine();
        if (string.IsNullOrEmpty(requestLine)) return;

        string[] tokens = requestLine.Split(' ');
        string method = tokens[0];
        string url = tokens[1];

        string path = url.Contains("?") ? url.Substring(0, url.IndexOf("?")) : url;
        string queryString = url.Contains("?") ? url.Substring(url.IndexOf("?") + 1) : "";

        while (!string.IsNullOrEmpty(reader.ReadLine())) { }

        if (method == "GET" && path == "/greeting")
        {
            HandleGreetingRequest(writer, queryString);
        }
        else if (method == "GET" && path == "/info")
        {
            HandleInfoRequest(writer, queryString);
        }
        else
        {
            SendNotFoundResponse(writer);
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Error: {ex.Message}");
    }
    finally
    {
        client.Close();
    }
}
```

### 9. Kết luận
- Phần 4 đã hoàn thiện thêm các chức năng xử lý query string và tạo nội dung động cho Web server.
- Server hiện có thể trả về các trang HTML dựa trên thamía số từ client.
- Bài tập mở rộng giúp người học củng cố kiến thức về xử lý chuỗi, giao tiếp mạng, và tạo phản hồi HTTP.

### 10. Bài tập về nhà
- Thêm endpoint `/calculate?num1=5&num2=10` để thực hiện phép tính cộng và trả về kết quả dạng HTML: "Result: 15".
- Thêm kiểm tra lỗi khi `num1` hoặc `num2` không phải là số.
- Cải tiến server để hỗ trợ phương thức POST (yêu cầu nâng cao).