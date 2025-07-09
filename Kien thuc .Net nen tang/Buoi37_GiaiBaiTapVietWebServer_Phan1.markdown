# Buổi 37: Giải bài tập viết Web server (phần 1)

## Mục tiêu
- Hiểu cách xây dựng một Web server cơ bản sử dụng .NET.
- Tìm hiểu cách lắng nghe kết nối từ client và xử lý yêu cầu HTTP.
- Thực hành viết code để tạo một Web server đơn giản.

## Nội dung chính

### 1. Tổng quan về Web server
- **Web server** là một phần mềm hoặc phần cứng chịu trách nhiệm nhận yêu cầu từ client (thường là trình duyệt web) qua giao thức HTTP/HTTPS, xử lý yêu cầu đó và trả về phản hồi (response) tương ứng.
- Trong bài này, chúng ta sẽ xây dựng một Web server đơn giản bằng C# sử dụng .NET Framework, tập trung vào việc:
  - Lắng nghe kết nối từ client.
  - Nhận yêu cầu HTTP.
  - Trả về phản hồi cơ bản (ví dụ: một trang HTML đơn giản).

### 2. Các bước xây dựng Web server cơ bản
1. **Khởi tạo TcpListener**:
   - Sử dụng lớp `TcpListener` trong namespace `System.Net.Sockets` để lắng nghe kết nối từ client.
   - Cấu hình địa chỉ IP và cổng (port) mà server sẽ lắng nghe.
2. **Chấp nhận kết nối**:
   - Sử dụng phương thức `AcceptTcpClient()` để chấp nhận kết nối từ client.
3. **Đọc yêu cầu từ client**:
   - Sử dụng `NetworkStream` để đọc dữ liệu từ client (yêu cầu HTTP).
4. **Gửi phản hồi**:
   - Tạo phản hồi HTTP (bao gồm header và body) và gửi lại cho client.
5. **Đóng kết nối**:
   - Đóng kết nối sau khi xử lý xong yêu cầu.

### 3. Code ví dụ: Web server cơ bản

Dưới đây là đoạn code được trình bày trong video, minh họa cách xây dựng một Web server đơn giản:

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
        // Khởi tạo TcpListener với địa chỉ IP bất kỳ và cổng 8080
        TcpListener server = new TcpListener(IPAddress.Any, 8080);
        
        try
        {
            // Bắt đầu lắng nghe kết nối
            server.Start();
            Console.WriteLine("Web server đang chạy trên cổng 8080...");
            
            while (true)
            {
                // Chấp nhận kết nối từ client
                TcpClient client = server.AcceptTcpClient();
                Console.WriteLine("Client đã kết nối!");
                
                // Xử lý yêu cầu trong một luồng riêng (tạm thời xử lý trực tiếp)
                HandleClient(client);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Lỗi: " + ex.Message);
        }
        finally
        {
            server.Stop();
        }
    }

    static void HandleClient(TcpClient client)
    {
        try
        {
            // Lấy stream để đọc/ghi dữ liệu
            NetworkStream stream = client.GetStream();

            // Đọc yêu cầu từ client
            byte[] buffer = new byte[1024];
            int bytesRead = stream.Read(buffer, 0, buffer.Length);
            string request = Encoding.UTF8.GetString(buffer, 0, bytesRead);
            Console.WriteLine("Yêu cầu từ client:\n" + request);

            // Chuẩn bị phản hồi HTTP
            string html = "<html><body><h1>Chào mừng đến với Web server của tôi!</h1></body></html>";
            string response = "HTTP/1.1 200 OK\r\n" +
                             "Content-Type: text/html\r\n" +
                             "Content-Length: " + html.Length + "\r\n" +
                             "\r\n" +
                             html;

            // Gửi phản hồi về client
            byte[] responseBytes = Encoding.UTF8.GetBytes(response);
            stream.Write(responseBytes, 0, responseBytes.Length);

            // Đóng kết nối
            stream.Close();
            client.Close();
        }
        catch (Exception ex)
        {
            Console.WriteLine("Lỗi khi xử lý client: " + ex.Message);
        }
    }
}
```

#### Giải thích code:
- **TcpListener**: Lắng nghe kết nối trên cổng 8080 với địa chỉ IP bất kỳ (`IPAddress.Any`).
- **AcceptTcpClient**: Chấp nhận kết nối từ client và trả về một đối tượng `TcpClient`.
- **NetworkStream**: Dùng để đọc yêu cầu từ client và gửi phản hồi.
- **Yêu cầu HTTP**: Yêu cầu từ client (thường là một chuỗi văn bản chứa phương thức HTTP, đường dẫn, header, v.v.) được đọc từ stream.
- **Phản hồi HTTP**: Bao gồm:
  - Dòng trạng thái: `HTTP/1.1 200 OK`.
  - Header: `Content-Type` và `Content-Length`.
  - Body: Nội dung HTML đơn giản.
- **Đóng kết nối**: Sau khi gửi phản hồi, stream và client được đóng để giải phóng tài nguyên.

### 4. Chạy và kiểm tra Web server
- **Cách chạy**:
  1. Chạy chương trình trên Visual Studio hoặc sử dụng lệnh `dotnet run`.
  2. Mở trình duyệt và truy cập `http://localhost:8080`.
  3. Kết quả: Trình duyệt sẽ hiển thị trang HTML với nội dung "Chào mừng đến với Web server của tôi!".
- **Kiểm tra console**:
  - Console sẽ hiển thị thông báo khi server khởi động và khi có client kết nối.
  - Yêu cầu HTTP từ client cũng được in ra console.

### 5. Một số lưu ý
- **Xử lý đồng thời nhiều client**:
  - Code trên chỉ xử lý một client tại một thời điểm. Để hỗ trợ nhiều client, cần sử dụng đa luồng (thread) hoặc lập trình bất đồng bộ (async/await).
- **Bảo mật**:
  - Web server này chỉ là demo, không nên dùng trong môi trường sản xuất vì thiếu các cơ chế bảo mật (HTTPS, kiểm tra đầu vào, v.v.).
- **Hiệu suất**:
  - Để cải thiện hiệu suất, có thể sử dụng các framework như ASP.NET thay vì viết Web server từ đầu.

### 6. Bài tập thực hành
1. **Mở rộng phản hồi**:
   - Thêm các trang HTML khác nhau dựa trên đường dẫn trong yêu cầu HTTP (ví dụ: `/about` trả về trang "Giới thiệu").
2. **Xử lý phương thức POST**:
   - Xử lý yêu cầu HTTP POST và trả về dữ liệu mà client gửi lên.
3. **Ghi log**:
   - Ghi lại các yêu cầu từ client vào một file log (bao gồm thời gian, địa chỉ IP client, yêu cầu).

## Kết luận
- Bài học này giúp hiểu cơ bản về cách hoạt động của một Web server và cách xây dựng nó bằng C#.
- Phần tiếp theo sẽ tập trung vào việc cải tiến Web server, xử lý nhiều client đồng thời và thêm các tính năng nâng cao.