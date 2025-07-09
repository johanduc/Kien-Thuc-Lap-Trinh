# Buổi 36: Lập trình mạng với Socket

## Mục tiêu
- Hiểu khái niệm về lập trình mạng và giao tiếp client-server.
- Nắm vững cách sử dụng **Socket** trong .NET để xây dựng ứng dụng mạng cơ bản.
- Thực hành xây dựng một ứng dụng chat đơn giản sử dụng giao thức TCP.

## Nội dung chính

### 1. Giới thiệu về Socket
- **Socket** là một cơ chế giao tiếp mạng cho phép các máy tính trao đổi dữ liệu với nhau qua giao thức TCP hoặc UDP.
- Trong .NET, lớp `System.Net.Sockets.Socket` cung cấp các phương thức để lập trình mạng ở mức thấp.
- Ứng dụng phổ biến: chat, truyền file, streaming, game online, v.v.

### 2. Các khái niệm cơ bản
- **TCP (Transmission Control Protocol)**:
  - Đảm bảo dữ liệu được gửi và nhận chính xác, theo thứ tự.
  - Phù hợp cho ứng dụng yêu cầu độ tin cậy cao (như chat).
- **UDP (User Datagram Protocol)**:
  - Nhanh hơn TCP nhưng không đảm bảo dữ liệu được gửi đến đúng thứ tự hoặc đầy đủ.
  - Thích hợp cho streaming, game thời gian thực.
- **Client-Server Model**:
  - **Server**: Máy chủ lắng nghe kết nối từ client.
  - **Client**: Kết nối đến server để gửi/nhận dữ liệu.

### 3. Các lớp chính trong .NET để lập trình Socket
- `Socket`: Lớp chính để tạo và quản lý kết nối mạng.
- `IPAddress`: Đại diện cho địa chỉ IP.
- `IPEndPoint`: Kết hợp địa chỉ IP và cổng (port) để xác định điểm kết nối.
- `TcpListener`: Đơn giản hóa việc tạo server TCP.
- `TcpClient`: Đơn giản hóa việc tạo client TCP.

### 4. Quy trình xây dựng ứng dụng mạng với Socket
#### Server
1. Tạo `TcpListener` để lắng nghe kết nối từ client.
2. Chấp nhận kết nối từ client bằng phương thức `AcceptTcpClient`.
3. Đọc/ghi dữ liệu qua `NetworkStream`.
4. Đóng kết nối khi hoàn tất.

#### Client
1. Tạo `TcpClient` và kết nối đến server bằng địa chỉ IP và cổng.
2. Gửi/nhận dữ liệu qua `NetworkStream`.
3. Đóng kết nối khi hoàn tất.

### 5. Ví dụ thực hành: Ứng dụng chat đơn giản (Server-Client)

#### Mã nguồn Server
```csharp
using System;
using System.Net;
using System.Net.Sockets;
using System.Text;

class Server
{
    static void Main(string[] args)
    {
        TcpListener server = null;
        try
        {
            // Thiết lập server
            IPAddress ipAddress = IPAddress.Parse("127.0.0.1");
            int port = 8080;
            server = new TcpListener(ipAddress, port);
            server.Start();
            Console.WriteLine("Server started...");

            while (true)
            {
                // Chấp nhận kết nối từ client
                TcpClient client = server.AcceptTcpClient();
                Console.WriteLine("Client connected!");

                // Lấy luồng dữ liệu
                NetworkStream stream = client.GetStream();

                // Nhận dữ liệu từ client
                byte[] buffer = new byte[1024];
                int bytesRead = stream.Read(buffer, 0, buffer.Length);
                string message = Encoding.UTF8.GetString(buffer, 0, bytesRead);
                Console.WriteLine("Client: " + message);

                // Gửi phản hồi về client
                string response = "Server received: " + message;
                byte[] responseData = Encoding.UTF8.GetBytes(response);
                stream.Write(responseData, 0, responseData.Length);

                // Đóng kết nối
                stream.Close();
                client.Close();
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        finally
        {
            server?.Stop();
        }
    }
}
```

#### Mã nguồn Client
```csharp
using System;
using System.Net.Sockets;
using System.Text;

class Client
{
    static void Main(string[] args)
    {
        try
        {
            // Kết nối đến server
            TcpClient client = new TcpClient("127.0.0.1", 8080);
            Console.WriteLine("Connected to server...");

            // Lấy luồng dữ liệu
            NetworkStream stream = client.GetStream();

            // Gửi dữ liệu đến server
            string message = "Hello from client!";
            byte[] data = Encoding.UTF8.GetBytes(message);
            stream.Write(data, 0, data.Length);
            Console.WriteLine("Sent: " + message);

            // Nhận phản hồi từ server
            byte[] buffer = new byte[1024];
            int bytesRead = stream.Read(buffer, 0, buffer.Length);
            string response = Encoding.UTF8.GetString(buffer, 0, bytesRead);
            Console.WriteLine("Server: " + response);

            // Đóng kết nối
            stream.Close();
            client.Close();
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
    }
}
```

### 6. Hướng dẫn chạy ví dụ
1. Chạy chương trình Server trước để lắng nghe kết nối.
2. Chạy chương trình Client để gửi tin nhắn đến Server.
3. Server nhận tin nhắn, hiển thị và gửi phản hồi lại Client.
4. Client nhận phản hồi từ Server và hiển thị.

### 7. Một số lưu ý
- **Xử lý ngoại lệ**: Luôn bao bọc code trong `try-catch` để xử lý lỗi mạng.
- **Đa luồng**: Để server hỗ trợ nhiều client cùng lúc, cần sử dụng đa luồng (threading) hoặc async/await.
- **Đóng kết nối**: Đảm bảo đóng các `Stream` và `TcpClient`/`TcpListener` sau khi sử dụng.
- **Mã hóa dữ liệu**: Sử dụng `Encoding.UTF8` để chuyển đổi giữa chuỗi và byte.

### 8. Bài tập thực hành
1. Mở rộng ứng dụng chat để hỗ trợ nhiều client cùng lúc bằng cách sử dụng đa luồng.
2. Thêm giao diện đồ họa (WinForms hoặc WPF) cho ứng dụng chat.
3. Thử triển khai ứng dụng sử dụng giao thức UDP thay vì TCP.

### 9. Tài liệu tham khảo
- Tài liệu chính thức của Microsoft về `System.Net.Sockets`: [Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets)
- Hướng dẫn lập trình mạng với .NET: [Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/fundamentals/networking/)