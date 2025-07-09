# Buổi 41: Sử dụng thư viện HttpClient

## Mục tiêu

- Hiểu cách sử dụng thư viện `HttpClient` trong .NET để thực hiện các yêu cầu HTTP (GET, POST, PUT, DELETE).
- Tìm hiểu cách gửi và nhận dữ liệu từ API RESTful.
- Xử lý phản hồi từ máy chủ và các tình huống lỗi.

## Nội dung chính

### 1. Giới thiệu về HttpClient

- `HttpClient` là một lớp trong namespace `System.Net.Http`, được sử dụng để gửi các yêu cầu HTTP và nhận phản hồi từ các tài nguyên trên web (thường là API).
- Ưu điểm:
  - Hỗ trợ các phương thức HTTP phổ biến: GET, POST, PUT, DELETE, v.v.
  - Xử lý bất đồng bộ (async/await) để cải thiện hiệu suất.
  - Dễ dàng cấu hình header, query parameter, và nội dung yêu cầu.
- Lưu ý:
  - `HttpClient` là một tài nguyên nặng, nên được sử dụng dưới dạng singleton hoặc tái sử dụng trong ứng dụng (không tạo mới liên tục).
  - Cần xử lý lỗi và quản lý tài nguyên đúng cách.

### 2. Các bước cơ bản sử dụng HttpClient

1. **Khởi tạo HttpClient**:
   - Tạo một instance của `HttpClient`.
   - Có thể cấu hình thêm các thông số như base address, timeout, hoặc header mặc định.
2. **Gửi yêu cầu HTTP**:
   - Sử dụng các phương thức như `GetAsync`, `PostAsync`, `PutAsync`, `DeleteAsync`.
   - Truyền URL và các tham số cần thiết (nếu có).
3. **Xử lý phản hồi**:
   - Kiểm tra mã trạng thái HTTP (`HttpStatusCode`) để xác định yêu cầu thành công hay thất bại.
   - Đọc nội dung phản hồi bằng các phương thức như `ReadAsStringAsync`, `ReadAsAsync<T>`.
4. **Quản lý lỗi**:
   - Sử dụng try-catch để xử lý ngoại lệ (`HttpRequestException`, `TaskCanceledException`, v.v.).
   - Kiểm tra `IsSuccessStatusCode` để đảm bảo yêu cầu thành công.

### 3. Ví dụ minh họa

Video sử dụng một API giả lập (JSONPlaceholder: `https://jsonplaceholder.typicode.com`) để minh họa các thao tác với `HttpClient`.

#### 3.1. Yêu cầu GET

- **Mục đích**: Lấy danh sách bài viết (posts) từ API.
- **Code ví dụ**:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        using var client = new HttpClient();
        try
        {
            HttpResponseMessage response = await client.GetAsync("https://jsonplaceholder.typicode.com/posts");
            response.EnsureSuccessStatusCode(); // Ném ngoại lệ nếu không thành công
            string responseBody = await response.Content.ReadAsStringAsync();
            Console.WriteLine(responseBody);
        }
        catch (HttpRequestException e)
        {
            Console.WriteLine($"Lỗi khi gửi yêu cầu: {e.Message}");
        }
    }
}
```

- **Giải thích**:
  - `GetAsync`: Gửi yêu cầu GET tới URL được chỉ định.
  - `EnsureSuccessStatusCode`: Kiểm tra mã trạng thái HTTP (200-299 là thành công).
  - `ReadAsStringAsync`: Đọc nội dung phản hồi dưới dạng chuỗi (JSON trong trường hợp này).

#### 3.2. Yêu cầu POST

- **Mục đích**: Tạo một bài viết mới trên API.
- **Code ví dụ**:

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        using var client = new HttpClient();
        var postData = new
        {
            title = "Tiêu đề bài viết",
            body = "Nội dung bài viết",
            userId = 1
        };
        string json = System.Text.Json.JsonSerializer.Serialize(postData);
        var content = new StringContent(json, Encoding.UTF8, "application/json");

        try
        {
            HttpResponseMessage response = await client.PostAsync("https://jsonplaceholder.typicode.com/posts", content);
            response.EnsureSuccessStatusCode();
            string responseBody = await response.Content.ReadAsStringAsync();
            Console.WriteLine(responseBody);
        }
        catch (HttpRequestException e)
        {
            Console.WriteLine($"Lỗi khi gửi yêu cầu: {e.Message}");
        }
    }
}
```

- **Giải thích**:
  - Tạo đối tượng JSON từ một anonymous object và serialize thành chuỗi.
  - `StringContent`: Đóng gói dữ liệu JSON với kiểu nội dung `application/json`.
  - `PostAsync`: Gửi yêu cầu POST với nội dung JSON.

#### 3.3. Yêu cầu PUT

- **Mục đích**: Cập nhật một bài viết hiện có.
- **Code ví dụ**:

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        using var client = new HttpClient();
        var postData = new
        {
            id = 1,
            title = "Tiêu đề đã cập nhật",
            body = "Nội dung đã cập nhật",
            userId = 1
        };
        string json = System.Text.Json.JsonSerializer.Serialize(postData);
        var content = new StringContent(json, Encoding.UTF8, "application/json");

        try
        {
            HttpResponseMessage response = await client.PutAsync("https://jsonplaceholder.typicode.com/posts/1", content);
            response.EnsureSuccessStatusCode();
            string responseBody = await response.Content.ReadAsStringAsync();
            Console.WriteLine(responseBody);
        }
        catch (HttpRequestException e)
        {
            Console.WriteLine($"Lỗi khi gửi yêu cầu: {e.Message}");
        }
    }
}
```

- **Giải thích**:
  - Tương tự POST, nhưng sử dụng `PutAsync` để cập nhật tài nguyên.
  - URL bao gồm ID của tài nguyên cần cập nhật (`posts/1`).

#### 3.4. Yêu cầu DELETE

- **Mục đích**: Xóa một bài viết.
- **Code ví dụ**:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        using var client = new HttpClient();
        try
        {
            HttpResponseMessage response = await client.DeleteAsync("https://jsonplaceholder.typicode.com/posts/1");
            response.EnsureSuccessStatusCode();
            Console.WriteLine("Xóa bài viết thành công!");
        }
        catch (HttpRequestException e)
        {
            Console.WriteLine($"Lỗi khi gửi yêu cầu: {e.Message}");
        }
    }
}
```

- **Giải thích**:
  - `DeleteAsync`: Gửi yêu cầu DELETE để xóa tài nguyên.
  - Không cần gửi nội dung trong yêu cầu DELETE.

### 4. Xử lý phản hồi JSON

- Để xử lý dữ liệu JSON phức tạp, có thể sử dụng `System.Text.Json` hoặc thư viện `Newtonsoft.Json` để deserialize thành đối tượng C#.
- Ví dụ deserialize danh sách bài viết:

```csharp
using System;
using System.Net.Http;
using System.Text.Json;
using System.Threading.Tasks;
using System.Collections.Generic;

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Body { get; set; }
    public int UserId { get; set; }
}

class Program
{
    static async Task Main(string[] args)
    {
        using var client = new HttpClient();
        try
        {
            HttpResponseMessage response = await client.GetAsync("https://jsonplaceholder.typicode.com/posts");
            response.EnsureSuccessStatusCode();
            string responseBody = await response.Content.ReadAsStringAsync();
            List<Post> posts = JsonSerializer.Deserialize<List<Post>>(responseBody);
            foreach (var post in posts)
            {
                Console.WriteLine($"ID: {post.Id}, Title: {post.Title}");
            }
        }
        catch (HttpRequestException e)
        {
            Console.WriteLine($"Lỗi khi gửi yêu cầu: {e.Message}");
        }
    }
}
```

- **Giải thích**:
  - Tạo lớp `Post` để ánh xạ dữ liệu JSON.
  - `JsonSerializer.Deserialize`: Chuyển chuỗi JSON thành danh sách các đối tượng `Post`.

### 5. Một số lưu ý quan trọng

- **Tái sử dụng HttpClient**:
  - Không nên tạo mới `HttpClient` cho mỗi yêu cầu vì có thể gây cạn kiệt socket.
  - Sử dụng `using` để đảm bảo tài nguyên được giải phong đúng cách, hoặc dùng `HttpClient` như một singleton.
- **Xử lý timeout**:
  - Có thể cấu hình `HttpClient.Timeout` để giới hạn thời gian chờ phản hồi.
- **Xử lý lỗi**:
  - Kiểm tra mã trạng thái HTTP (`response.StatusCode`) và xử lý các trường hợp như 404, 500.
  - Sử dụng try-catch để bắt các ngoại lệ như `HttpRequestException` hoặc `TaskCanceledException` (timeout).
- **Cấu hình header**:
  - Có thể thêm header (ví dụ: Authorization) bằng `client.DefaultRequestHeaders.Add`.

### 6. Bài tập thực hành

1. **Bài tập 1**: Viết chương trình sử dụng `HttpClient` để lấy danh sách người dùng từ API `https://jsonplaceholder.typicode.com/users` và hiển thị tên cùng email của họ.
2. **Bài tập 2**: Tạo một bài viết mới bằng phương thức POST, sau đó cập nhật bài viết đó bằng PUT và cuối cùng xóa nó bằng DELETE.
3. **Bài tập 3**: Viết chương trình gửi yêu cầu GET với query parameter, ví dụ: `https://jsonplaceholder.typicode.com/posts?userId=1` để lấy bài viết của user có ID là 1.
4. **Bài tập 4**: Sử dụng `HttpClient` để gọi một API thật (ví dụ: API thời tiết) và hiển thị kết quả.

### 7. Tài liệu tham khảo

- Tài liệu chính thức của Microsoft về `HttpClient`: https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient
- API giả lập JSONPlaceholder: https://jsonplaceholder.typicode.com/
- Hướng dẫn sử dụng `System.Text.Json`: https://docs.microsoft.com/en-us/dotnet/standard/serialization/system-text-json-how-to
