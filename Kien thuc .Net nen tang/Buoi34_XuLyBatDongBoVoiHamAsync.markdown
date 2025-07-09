# Buổi 34: Xử lý bất đồng bộ với hàm async

## Mục tiêu
- Hiểu về lập trình bất đồng bộ (asynchronous programming) trong .NET.
- Nắm vững cách sử dụng từ khóa `async` và `await` để xử lý các tác vụ bất đồng bộ.
- Áp dụng xử lý bất đồng bộ trong các tình huống thực tế như gọi API, đọc/ghi file, hoặc thực hiện các tác vụ tốn thời gian.

## Nội dung chính

### 1. Tổng quan về lập trình bất đồng bộ
- **Đồng bộ (Synchronous)**: Các tác vụ được thực hiện tuần tự, tác vụ tiếp theo phải đợi tác vụ trước hoàn thành.
- **Bất đồng bộ (Asynchronous)**: Các tác vụ có thể chạy song song, không cần đợi nhau hoàn thành, giúp cải thiện hiệu suất và trải nghiệm người dùng.
- Trong .NET, lập trình bất đồng bộ dựa trên **Task-based Asynchronous Pattern (TAP)**, sử dụng các từ khóa `async` và `await`.

### 2. Từ khóa `async` và `await`
- **`async`**: Được sử dụng trong khai báo phương thức để chỉ ra rằng phương thức này là bất đồng bộ. Một phương thức `async` thường trả về `Task` hoặc `Task<T>`.
- **`await`**: Dùng để chờ kết quả của một tác vụ bất đồng bộ mà không chặn luồng (thread) hiện tại. Chỉ được sử dụng trong các phương thức được đánh dấu `async`.

### 3. Cú pháp cơ bản
```csharp
public async Task<string> GetDataAsync()
{
    // Giả lập tác vụ tốn thời gian (ví dụ: gọi API)
    await Task.Delay(2000); // Chờ 2 giây
    return "Dữ liệu đã được lấy";
}
```

- Phương thức trên trả về một `Task<string>`, sử dụng `await` để chờ tác vụ hoàn thành.
- Khi gọi phương thức này:
```csharp
public async Task CallAsyncMethod()
{
    string result = await GetDataAsync();
    Console.WriteLine(result);
}
```

### 4. Ví dụ thực tế

#### Ví dụ 1: Gọi API bất đồng bộ
```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class Program
{
    private static readonly HttpClient client = new HttpClient();

    public static async Task Main(string[] args)
    {
        string url = "https://api.example.com/data";
        string result = await GetApiDataAsync(url);
        Console.WriteLine(result);
    }

    public static async Task<string> GetApiDataAsync(string url)
    {
        try
        {
            HttpResponseMessage response = await client.GetAsync(url);
            response.EnsureSuccessStatusCode();
            return await response.Content.ReadAsStringAsync();
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"Lỗi khi gọi API: {ex.Message}");
            return string.Empty;
        }
    }
}
```
- **Giải thích**:
  - Sử dụng `HttpClient` để gọi API.
  - `await client.GetAsync(url)`: Gọi API bất đồng bộ.
  - `await response.Content.ReadAsStringAsync()`: Đọc nội dung phản hồi bất đồng bộ.
  - Xử lý ngoại lệ để đảm bảo chương trình không bị crash nếu API gặp lỗi.

#### Ví dụ 2: Đọc file bất đồng bộ
```csharp
using System;
using System.IO;
using System.Threading.Tasks;

public class Program
{
    public static async Task Main(string[] args)
    {
        string filePath = "example.txt";
        string content = await ReadFileAsync(filePath);
        Console.WriteLine(content);
    }

    public static async Task<string> ReadFileAsync(string filePath)
    {
        try
        {
            using StreamReader reader = new StreamReader(filePath);
            return await reader.ReadToEndAsync();
        }
        catch (IOException ex)
        {
            Console.WriteLine($"Lỗi khi đọc file: {ex.Message}");
            return string.Empty;
        }
    }
}
```
- **Giải thích**:
  - Sử dụng `StreamReader` để đọc file.
  - `await reader.ReadToEndAsync()`: Đọc toàn bộ nội dung file bất đồng bộ.
  - Xử lý ngoại lệ để quản lý lỗi khi file không tồn tại hoặc không thể đọc.

#### Ví dụ 3: Chạy nhiều tác vụ bất đồng bộ song song
```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

public class Program
{
    public static async Task Main(string[] args)
    {
        Task<int> task1 = ProcessDataAsync(1, 2000);
        Task<int> task2 = ProcessDataAsync(2, 3000);
        Task<int> task3 = ProcessDataAsync(3, 1000);

        // Chờ tất cả các task hoàn thành
        int[] results = await Task.WhenAll(task1, task2, task3);

        foreach (var result in results)
        {
            Console.WriteLine($"Kết quả: {result}");
        }
    }

    public static async Task<int> ProcessDataAsync(int id, int delay)
    {
        await Task.Delay(delay); // Giả lập thời gian xử lý
        return id * 10;
    }
}
```
- **Giải thích**:
  - `Task.WhenAll`: Chờ tất cả các tác vụ bất đồng bộ hoàn thành và trả về kết quả.
  - Các tác vụ chạy song song, giúp tiết kiệm thời gian so với chạy tuần tự.

### 5. Một số lưu ý quan trọng
- **Không chặn luồng chính**: Tránh sử dụng `.Result` hoặc `.Wait()` trên một `Task` trong phương thức `async`, vì có thể gây ra deadlock.
- **Xử lý ngoại lệ**: Luôn bọc các tác vụ bất đồng bộ trong `try-catch` để xử lý lỗi.
- **Hiệu suất**: Sử dụng `async`/`await` đúng cách giúp cải thiện hiệu suất, đặc biệt với các tác vụ I/O-bound (như gọi API, đọc/ghi file).
- **ConfigureAwait(false)**: Trong các thư viện, sử dụng `ConfigureAwait(false)` để tránh quay lại ngữ cảnh đồng bộ hóa ban đầu, trừ khi cần thiết.

### 6. Bài tập ví dụ
#### Bài tập 1: Gọi nhiều API song song
Viết một chương trình gọi 3 API khác nhau và hiển thị kết quả khi tất cả hoàn thành.
```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;

public class Program
{
    private static readonly HttpClient client = new HttpClient();

    public static async Task Main(string[] args)
    {
        string[] urls = new[]
        {
            "https://api.example.com/data1",
            "https://api.example.com/data2",
            "https://api.example.com/data3"
        };

        List<Task<string>> tasks = new List<Task<string>>();
        foreach (var url in urls)
        {
            tasks.Add(GetApiDataAsync(url));
        }

        string[] results = await Task.WhenAll(tasks);
        for (int i = 0; i < results.Length; i++)
        {
            Console.WriteLine($"API {i + 1}: {results[i]}");
        }
    }

    public static async Task<string> GetApiDataAsync(string url)
    {
        try
        {
            HttpResponseMessage response = await client.GetAsync(url);
            response.EnsureSuccessStatusCode();
            return await response.Content.ReadAsStringAsync();
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"Lỗi khi gọi API {url}: {ex.Message}");
            return string.Empty;
        }
    }
}
```

#### Bài tập 2: Kết hợp đọc file và gọi API
Viết một chương trình đọc danh sách URL từ một file text, sau đó gọi các API tương ứng và lưu kết quả vào một file mới.
```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Threading.Tasks;

public class Program
{
    private static readonly HttpClient client = new HttpClient();

    public static async Task Main(string[] args)
    {
        string inputFilePath = "urls.txt";
        string outputFilePath = "results.txt";

        string[] urls = await File.ReadAllLinesAsync(inputFilePath);
        List<Task<string>> tasks = new List<Task<string>>();

        foreach (var url in urls)
        {
            tasks.Add(GetApiDataAsync(url));
        }

        string[] results = await Task.WhenAll(tasks);
        await File.WriteAllLinesAsync(outputFilePath, results);
        Console.WriteLine("Kết quả đã được lưu vào file results.txt");
    }

    public static async Task<string> GetApiDataAsync(string url)
    {
        try
        {
            HttpResponseMessage response = await client.GetAsync(url);
            response.EnsureSuccessStatusCode();
            return await response.Content.ReadAsStringAsync();
        }
        catch (HttpRequestException ex)
        {
            return $"Lỗi khi gọi API {url}: {ex.Message}";
        }
    }
}
```

### 7. Kết luận
- Lập trình bất đồng bộ với `async` và `await` là công cụ mạnh mẽ trong .NET để xử lý các tác vụ tốn thời gian mà không chặn luồng chính.
- Các ví dụ như gọi API, đọc/ghi file, hoặc chạy nhiều tác vụ song song giúp hiểu rõ cách áp dụng trong thực tế.
- Luôn chú ý xử lý ngoại lệ và tối ưu hóa hiệu suất khi sử dụng `async`/`await`.