# Buổi 38: Giải bài tập viết Web Server (Phần 2)

## Mục tiêu
- Tiếp tục giải bài tập viết Web Server trong .NET.
- Xử lý các yêu cầu HTTP và trả về phản hồi phù hợp.
- Tìm hiểu cách phân tích yêu cầu từ client, xử lý các phương thức HTTP (GET, POST, v.v.).
- Tích hợp thêm các tính năng như xử lý file tĩnh, trả về nội dung động.

## Nội dung chính

### 1. Tổng quan về bài tập
- **Mục đích**: Xây dựng một Web Server đơn giản bằng .NET sử dụng `TcpListener` để lắng nghe các yêu cầu HTTP từ client và trả về phản hồi.
- **Yêu cầu**:
  - Xử lý các yêu cầu HTTP cơ bản (GET, POST).
  - Phục vụ các file tĩnh (HTML, CSS, JS, hình ảnh, v.v.).
  - Trả về phản hồi động (ví dụ: nội dung HTML được sinh ra từ code).
  - Xử lý các mã trạng thái HTTP (200 OK, 404 Not Found, v.v.).
- **Tiếp nối từ Phần 1**: Ở phần 1, chúng ta đã thiết lập cơ bản Web Server với `TcpListener`, xử lý yêu cầu GET và trả về phản hồi đơn giản. Phần 2 sẽ tập trung vào xử lý file tĩnh và các phương thức HTTP khác.

### 2. Cấu trúc dự án
- **Dự án**: Một console application trong .NET.
- **Thư viện sử dụng**:
  - `System.Net` (cho `TcpListener`, `TcpClient`).
  - `System.IO` (để đọc file tĩnh).
  - `System.Text` (để xử lý chuỗi phản hồi).
- **Thư mục**:
  - `wwwroot`: Chứa các file tĩnh như `index.html`, `style.css`, `image.jpg`, v.v.

### 3. Các bước thực hiện

#### 3.1. Đọc và phân tích yêu cầu HTTP
- Yêu cầu HTTP từ client gửi đến server có định dạng:
  ```
  <Phương thức> <Đường dẫn> <Phiên bản HTTP>
  <Header1>: <Giá trị1>
  <Header2>: <Giá trị2>
  ...
  ```
  Ví dụ:
  ```
  GET /index.html HTTP/1.1
  Host: localhost:8080
  User-Agent: Mozilla/5.0
  ```

- **Code phân tích yêu cầu**:
  ```csharp
  using System;
  using System.IO;
  using System.Net;
  using System.Text;

  class HttpRequest
  {
      public string Method { get; set; }
      public string Path { get; set; }
      public string HttpVersion { get; set; }

      public static HttpRequest Parse(string request)
      {
          var lines = request.Split(new[] { "\r\n" }, StringSplitOptions.None);
          var requestLine = lines[0].Split(' ');
          return new HttpRequest
          {
              Method = requestLine[0],
              Path = requestLine[1],
              HttpVersion = requestLine[2]
          };
      }
  }
  ```

#### 3.2. Xử lý file tĩnh
- Khi client yêu cầu một file (ví dụ: `/index.html`), server sẽ:
  1. Kiểm tra file có tồn tại trong thư mục `wwwroot`.
  2. Đọc nội dung file.
  3. Trả về nội dung với mã trạng thái 200 (nếu file tồn tại) hoặc 404 (nếu không tồn tại).

- **Code xử lý file tĩnh**:
  ```csharp
  string ServeStaticFile(string path)
  {
      string filePath = Path.Combine("wwwroot", path.TrimStart('/'));
      if (File.Exists(filePath))
      {
          string content = File.ReadAllText(filePath);
          string contentType = GetContentType(filePath);
          return $"HTTP/1.1 200 OK\r\nContent-Type: {contentType}\r\n\r\n{content}";
      }
      return "HTTP/1.1 404 Not Found\r\nContent-Type: text/plain\r\n\r\nFile not found";
  }

  string GetContentType(string filePath)
  {
      string extension = Path.GetExtension(filePath).ToLower();
      return extension switch
      {
          ".html" => "text/html",
          ".css" => "text/css",
          ".js" => "application/javascript",
          ".jpg" => "image/jpeg",
          ".png" => "image/png",
          _ => "application/octet-stream"
      };
  }
  ```

#### 3.3. Xử lý yêu cầu POST
- Với yêu cầu POST, server cần đọc nội dung body của yêu cầu (nếu có) và xử lý dữ liệu.
- Ví dụ: Client gửi một form HTML với dữ liệu POST, server có thể trả về một phản hồi động dựa trên dữ liệu nhận được.

- **Code xử lý POST**:
  ```csharp
  string HandlePostRequest(string requestBody)
  {
      // Phân tích body của yêu cầu POST (ví dụ: dữ liệu form)
      // Ở đây chỉ trả về một phản hồi đơn giản
      return "HTTP/1.1 200 OK\r\nContent-Type: text/plain\r\n\r\nReceived POST data: " + requestBody;
  }
  ```

#### 3.4. Code chính của Web Server
- **Khởi tạo `TcpListener`**:
  ```csharp
  using System;
  using System.IO;
  using System.Net;
  using System.Net.Sockets;
  using System.Text;

  class Program
  {
      static void Main()
      {
          TcpListener server = new TcpListener(IPAddress.Any, 8080);
          server.Start();
          Console.WriteLine("Server started on port 8080...");

          while (true)
          {
              using (TcpClient client = server.AcceptTcpClient())
              using (NetworkStream stream = client.GetStream())
              {
                  // Đọc yêu cầu từ client
                  byte[] buffer = new byte[1024];
                  int bytesRead = stream.Read(buffer, 0, buffer.Length);
                  string request = Encoding.UTF8.GetString(buffer, 0, bytesRead);

                  // Phân tích yêu cầu
                  var httpRequest = HttpRequest.Parse(request);

                  // Xử lý yêu cầu
                  string response = httpRequest.Method switch
                  {
                      "GET" => ServeStaticFile(httpRequest.Path),
                      "POST" => HandlePostRequest(request),
                      _ => "HTTP/1.1 405 Method Not Allowed\r\nContent-Type: text/plain\r\n\r\nMethod not supported"
                  };

                  // Gửi phản hồi
                  byte[] responseBytes = Encoding.UTF8.GetBytes(response);
                  stream.Write(responseBytes, 0, responseBytes.Length);
              }
          }
      }
  }
  ```

### 4. Bài tập ví dụ
1. **Tạo file HTML tĩnh**:
   - Tạo file `index.html` trong thư mục `wwwroot`:
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <title>My Web Server</title>
     </head>
     <body>
         <h1>Welcome to my .NET Web Server!</h1>
     </body>
     </html>
     ```

2. **Thêm xử lý cho file ảnh**:
   - Cập nhật hàm `ServeStaticFile` để hỗ trợ file ảnh (`.jpg`, `.png`):
     ```csharp
     if (File.Exists(filePath))
     {
         if (Path.GetExtension(filePath).ToLower() == ".jpg" || Path.GetExtension(filePath).ToLower() == ".png")
         {
             byte[] fileBytes = File.ReadAllBytes(filePath);
             string contentType = GetContentType(filePath);
             return $"HTTP/1.1 200 OK\r\nContent-Type: {contentType}\r\nContent-Length: {fileBytes.Length}\r\n\r\n" + Encoding.UTF8.GetString(fileBytes);
         }
         // Xử lý file text như trước
         string content = File.ReadAllText(filePath);
         string contentType = GetContentType(filePath);
         return $"HTTP/1.1 200 OK\r\nContent-Type: {contentType}\r\n\r\n{content}";
     }
     ```

3. **Xử lý form POST**:
   - Tạo một form HTML đơn giản để gửi dữ liệu POST:
     ```html
     <form method="POST" action="/submit">
         <input type="text" name="username" />
         <input type="submit" value="Submit" />
     </form>
     ```
   - Cập nhật `HandlePostRequest` để xử lý dữ liệu form:
     ```csharp
     string HandlePostRequest(string requestBody)
     {
         // Giả sử body chứa dữ liệu dạng username=abc
         string username = requestBody.Split('=')[1];
         return $"HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n<h1>Hello, {username}!</h1>";
     }
     ```

### 5. Kiểm tra và chạy
- **Chạy server**:
  1. Tạo thư mục `wwwroot` và thêm file `index.html`.
  2. Chạy project console.
  3. Mở trình duyệt và truy cập `http://localhost:8080/index.html`.
- **Kiểm tra POST**:
  1. Tạo form HTML và gửi yêu cầu POST.
  2. Kiểm tra phản hồi từ server.

### 6. Lưu ý
- **Xử lý lỗi**: Thêm try-catch để xử lý các trường hợp file không tồn tại, yêu cầu không hợp lệ, v.v.
- **Hiệu suất**: Hiện tại server chỉ xử lý một yêu cầu tại một thời điểm. Để xử lý đồng thời nhiều yêu cầu, cần sử dụng đa luồng hoặc async/await.
- **Bảo mật**: Web Server này chỉ là demo, không nên dùng trong môi trường sản xuất vì thiếu các tính năng bảo mật.

## Tài liệu tham khảo
- Video YouTube: [Học lập trình .NET - Phần 38 - Giải bài tập viết Web Server (phần 2)](https://www.youtube.com/watch?v=Ns7-MptWchg&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=38)[](https://en.wikipedia.org/wiki/YouTube)
- Tài liệu .NET: [TcpListener Class](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcplistener)