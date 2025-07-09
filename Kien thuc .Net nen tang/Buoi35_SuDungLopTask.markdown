# Buổi 35: Sử dụng lớp Task trong .NET

## Mục tiêu
- Hiểu về lập trình bất đồng bộ (asynchronous programming) trong .NET.
- Nắm vững cách sử dụng lớp `Task` để thực hiện các tác vụ bất đồng bộ.
- Tìm hiểu các phương thức và thuộc tính cơ bản của lớp `Task`.
- Áp dụng `Task` trong các ví dụ thực tế.

## 1. Tổng quan về lớp Task
- **Task** là một lớp trong namespace `System.Threading.Tasks`, được sử dụng để đại diện cho một tác vụ bất đồng bộ.
- `Task` giúp thực hiện các công việc không chặn (non-blocking) luồng chính, cải thiện hiệu suất và trải nghiệm người dùng.
- Các đặc điểm chính:
  - **Task** đại diện cho một đơn vị công việc chạy bất đồng bộ.
  - Có thể trả về kết quả (`Task<T>`) hoặc không trả về kết quả (`Task`).
  - Hỗ trợ các thao tác như chờ đợi (`await`), tiếp tục (`ContinueWith`), xử lý lỗi, và hủy bỏ (`CancellationToken`).

## 2. Các cách khởi tạo Task
### 2.1. Sử dụng `Task.Run`
- `Task.Run` là cách đơn giản nhất để chạy một tác vụ bất đồng bộ.
- Ví dụ:
```csharp
Task.Run(() => Console.WriteLine("Chạy tác vụ bất đồng bộ!"));
```

### 2.2. Sử dụng `new Task`
- Khởi tạo một `Task` và gọi phương thức `Start()` để bắt đầu.
- Ví dụ:
```csharp
Task task = new Task(() => Console.WriteLine("Tác vụ được khởi tạo!"));
task.Start();
```

### 2.3. Sử dụng `Task.Factory.StartNew`
- Tạo và chạy `Task` với các tùy chọn cấu hình.
- Ví dụ:
```csharp
Task.Factory.StartNew(() => Console.WriteLine("Tác vụ từ TaskFactory!"));
```

## 3. Chờ Task hoàn thành
- **Phương thức `Wait`**: Chặn luồng hiện tại cho đến khi `Task` hoàn thành.
- **Phương thức `await`**: (Sử dụng với `async`) Không chặn luồng, chờ bất đồng bộ.
- Ví dụ sử dụng `await`:
```csharp
public async Task RunAsync()
{
    await Task.Run(() => Console.WriteLine("Tác vụ đang chạy!"));
    Console.WriteLine("Tác vụ đã hoàn thành!");
}
```

## 4. Task<T> - Task trả về giá trị
- `Task<T>` được sử dụng khi tác vụ trả về một giá trị kiểu `T`.
- Ví dụ:
```csharp
public async Task<int> CalculateAsync()
{
    return await Task.Run(() =>
    {
        int sum = 0;
        for (int i = 1; i <= 100; i++) sum += i;
        return sum;
    });
}
```

## 5. Xử lý lỗi trong Task
- Sử dụng `try-catch` để bắt ngoại lệ trong các tác vụ bất đồng bộ.
- Ví dụ:
```csharp
public async Task RunWithErrorHandlingAsync()
{
    try
    {
        await Task.Run(() => throw new Exception("Có lỗi xảy ra!"));
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Lỗi: {ex.Message}");
    }
}
```

## 6. Hủy bỏ Task với CancellationToken
- `CancellationToken` cho phép hủy bỏ một `Task` đang chạy.
- Ví dụ:
```csharp
public async Task RunWithCancellationAsync(CancellationToken token)
{
    try
    {
        await Task.Run(() =>
        {
            for (int i = 0; i < 10; i++)
            {
                token.ThrowIfCancellationRequested();
                Console.WriteLine($"Đang xử lý: {i}");
                Thread.Sleep(1000);
            }
        }, token);
    }
    catch (OperationCanceledException)
    {
        Console.WriteLine("Tác vụ đã bị hủy!");
    }
}
```

## 7. Task.WhenAll và Task.WhenAny
- **Task.WhenAll**: Chờ tất cả các `Task` trong danh sách hoàn thành.
- Ví dụ:
```csharp
public async Task RunMultipleTasksAsync()
{
    Task task1 = Task.Run(() => Console.WriteLine("Task 1"));
    Task task2 = Task.Run(() => Console.WriteLine("Task 2"));
    await Task.WhenAll(task1, task2);
    Console.WriteLine("Tất cả task đã hoàn thành!");
}
```

- **Task.WhenAny**: Chờ bất kỳ `Task` nào trong danh sách hoàn thành đầu tiên.
- Ví dụ:
```csharp
public async Task RunAnyTaskAsync()
{
    Task task1 = Task.Delay(2000);
    Task task2 = Task.Delay(1000);
    await Task.WhenAny(task1, task2);
    Console.WriteLine("Một task đã hoàn thành!");
}
```

## 8. Bài tập ví dụ
### Bài tập 1: Tính tổng bất đồng bộ
- Viết chương trình sử dụng `Task` để tính tổng các số từ 1 đến N bất đồng bộ.
```csharp
public async Task<int> CalculateSumAsync(int n)
{
    return await Task.Run(() =>
    {
        int sum = 0;
        for (int i = 1; i <= n; i++) sum += i;
        return sum;
    });
}
```

### Bài tập 2: Chạy nhiều Task đồng thời
- Tạo 3 `Task` in ra thông điệp khác nhau và sử dụng `Task.WhenAll` để chờ tất cả hoàn thành.
```csharp
public async Task RunMultipleMessagesAsync()
{
    Task task1 = Task.Run(() => Console.WriteLine("Tác vụ 1: Xin chào"));
    Task task2 = Task.Run(() => Console.WriteLine("Tác vụ 2: Lập trình .NET"));
    Task task3 = Task.Run(() => Console.WriteLine("Tác vụ 3: Task rất thú vị"));
    await Task.WhenAll(task1, task2, task3);
    Console.WriteLine("Hoàn thành tất cả tác vụ!");
}
```

### Bài tập 3: Hủy Task
- Viết chương trình chạy một `Task` đếm từ 1 đến 10, cho phép hủy sau 5 giây.
```csharp
public async Task RunCancellableTaskAsync()
{
    using (var cts = new CancellationTokenSource(5000))
    {
        try
        {
            await Task.Run(() =>
            {
                for (int i = 1; i <= 10; i++)
                {
                    cts.Token.ThrowIfCancellationRequested();
                    Console.WriteLine($"Đếm: {i}");
                    Thread.Sleep(1000);
                }
            }, cts.Token);
        }
        catch (OperationCanceledException)
        {
            Console.WriteLine("Tác vụ đã bị hủy sau 5 giây!");
        }
    }
}
```

## 9. Một số lưu ý khi sử dụng Task
- **Tránh chặn luồng chính**: Sử dụng `await` thay vì `Wait()` hoặc `.Result` để tránh deadlock.
- **Xử lý ngoại lệ**: Luôn bọc các tác vụ bất đồng bộ trong `try-catch`.
- **CancellationToken**: Sử dụng để quản lý việc hủy bỏ các tác vụ một cách an toàn.
- **Hiệu suất**: Sử dụng `Task.Run` cho các tác vụ nặng (CPU-bound), tránh lạm dụng cho các tác vụ I/O-bound.

## 10. Tài liệu tham khảo
- Tài liệu chính thức của Microsoft về `Task`: [Task Class](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task)
- Video YouTube: [Học lập trình .NET - Phần 35 - Sử dụng lớp Task](https://www.youtube.com/watch?v=irjccQZO4DM&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=35)