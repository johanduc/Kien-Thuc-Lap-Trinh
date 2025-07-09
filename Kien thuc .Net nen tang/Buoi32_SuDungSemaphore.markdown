# Buổi 32: Sử dụng Semaphore trong lập trình .NET

## Mục tiêu
- Hiểu khái niệm Semaphore và vai trò của nó trong lập trình đa luồng.
- Nắm được cách sử dụng Semaphore để kiểm soát truy cập tài nguyên chia sẻ.
- Thực hành thông qua ví dụ minh họa.

## Nội dung chính

### 1. Giới thiệu về Semaphore
- **Semaphore** là một cơ chế đồng bộ hóa trong lập trình đa luồng, được sử dụng để kiểm soát số lượng thread có thể truy cập đồng thời vào một tài nguyên chia sẻ.
- Semaphore hoạt động như một "bộ đếm" (counter):
  - Mỗi khi một thread muốn truy cập tài nguyên, nó phải "yêu cầu" một slot từ Semaphore (gọi là `Wait` hoặc `WaitOne`).
  - Nếu số slot còn lại lớn hơn 0, thread được phép truy cập, và bộ đếm giảm đi 1.
  - Khi thread hoàn thành công việc, nó "giải phóng" slot (gọi là `Release`), và bộ đếm tăng lên 1.
- Semaphore khác với **Mutex** ở chỗ:
  - Mutex chỉ cho phép **một thread** truy cập tài nguyên tại một thời điểm.
  - Semaphore cho phép **nhiều thread** (số lượng được định nghĩa trước) truy cập tài nguyên đồng thời.

### 2. Các loại Semaphore trong .NET
- **Semaphore**: Sử dụng trong phạm vi một ứng dụng (local).
- **SemaphoreSlim**: Một phiên bản nhẹ hơn của Semaphore, tối ưu cho các tình huống đồng bộ hóa nhanh trong một ứng dụng.
- **Named Semaphore**: Sử dụng để đồng bộ hóa giữa các tiến trình (process) khác nhau trên cùng một máy tính.

### 3. Cú pháp cơ bản
- **Khởi tạo Semaphore**:
  ```csharp
  Semaphore semaphore = new Semaphore(initialCount, maximumCount);
  ```
  - `initialCount`: Số lượng slot ban đầu khả dụng.
  - `maximumCount`: Số lượng slot tối đa mà Semaphore có thể quản lý.
- **Phương thức chính**:
  - `WaitOne()`: Yêu cầu một slot từ Semaphore. Nếu không có slot, thread sẽ bị chặn (block) cho đến khi có slot.
  - `Release()`: Giải phóng một slot, tăng bộ đếm lên 1.
  - `Release(int releaseCount)`: Giải phóng nhiều slot cùng lúc.

### 4. Ví dụ minh họa
#### Ví dụ 1: Sử dụng Semaphore để giới hạn số lượng thread truy cập tài nguyên
- Mô tả: Giả sử có một tài nguyên chỉ cho phép tối đa 3 thread truy cập cùng lúc. Chúng ta sẽ tạo 10 thread và sử dụng Semaphore để kiểm soát.
- Code mẫu:
  ```csharp
  using System;
  using System.Threading;

  class Program
  {
      static Semaphore semaphore = new Semaphore(3, 3); // Cho phép tối đa 3 thread truy cập

      static void Main(string[] args)
      {
          for (int i = 1; i <= 10; i++)
          {
              Thread thread = new Thread(AccessResource);
              thread.Name = $"Thread {i}";
              thread.Start();
          }
      }

      static void AccessResource()
      {
          Console.WriteLine($"{Thread.CurrentThread.Name} đang chờ để truy cập tài nguyên...");
          semaphore.WaitOne();

          Console.WriteLine($"{Thread.CurrentThread.Name} đã truy cập tài nguyên!");
          Thread.Sleep(1000); // Giả lập thời gian xử lý

          Console.WriteLine($"{Thread.CurrentThread.Name} đã hoàn thành và giải phóng tài nguyên.");
          semaphore.Release();
      }
  }
  ```
- **Giải thích**:
  - Semaphore được khởi tạo với `initialCount = 3` và `maximumCount = 3`, nghĩa là tối đa 3 thread có thể truy cập tài nguyên cùng lúc.
  - Mỗi thread gọi `WaitOne()` để yêu cầu slot. Nếu không còn slot, thread sẽ bị chặn.
  - Sau khi hoàn thành công việc (giả lập bằng `Thread.Sleep`), thread gọi `Release()` để giải phóng slot.
- **Kết quả chạy**:
  - Chỉ có tối đa 3 thread được in dòng "đã truy cập tài nguyên" cùng lúc.
  - Các thread còn lại sẽ đợi cho đến khi có slot được giải phóng.

#### Ví dụ 2: Sử dụng SemaphoreSlim
- **SemaphoreSlim** là một lựa chọn nhẹ hơn, phù hợp khi không cần đồng bộ hóa giữa các tiến trình.
- Code mẫu:
  ```csharp
  using System;
  using System.Threading;
  using System.Threading.Tasks;

  class Program
  {
      static SemaphoreSlim semaphoreSlim = new SemaphoreSlim(2, 2); // Cho phép tối đa 2 thread truy cập

      static async Task Main(string[] args)
      {
          Task[] tasks = new Task[5];
          for (int i = 1; i <= 5; i++)
          {
              int taskId = i;
              tasks[i - 1] = Task.Run(() => AccessResourceAsync(taskId));
          }
          await Task.WhenAll(tasks);
      }

      static async Task AccessResourceAsync(int taskId)
      {
          Console.WriteLine($"Task {taskId} đang chờ để truy cập tài nguyên...");
          await semaphoreSlim.WaitAsync();

          Console.WriteLine($"Task {taskId} đã truy cập tài nguyên!");
          await Task.Delay(1000); // Giả lập thời gian xử lý

          Console.WriteLine($"Task {taskId} đã hoàn thành và giải phóng tài nguyên.");
          semaphoreSlim.Release();
      }
  }
  ```
- **Giải thích**:
  - Sử dụng `SemaphoreSlim` với `initialCount = 2` và `maximumCount = 2`.
  - Sử dụng `WaitAsync` và `Task` để làm việc bất đồng bộ.
  - Kết quả tương tự ví dụ 1, nhưng chỉ tối đa 2 task truy cập tài nguyên cùng lúc.

### 5. Ứng dụng thực tế của Semaphore
- **Quản lý kết nối cơ sở dữ liệu**: Giới hạn số lượng kết nối đồng thời đến database.
- **Hạn chế truy cập API**: Kiểm soát số lượng yêu cầu gửi đến một API để tránh vượt quá giới hạn.
- **Quản lý tài nguyên hệ thống**: Đảm bảo không quá nhiều thread truy cập vào tài nguyên như file, network, hoặc bộ nhớ.

### 6. Lưu ý khi sử dụng Semaphore
- **Deadlock**: Nếu thread giữ slot mà không gọi `Release`, các thread khác có thể bị chặn mãi mãi.
- **Exception handling**: Luôn đảm bảo `Release` được gọi trong khối `finally` để tránh rò rỉ slot.
  ```csharp
  try
  {
      semaphore.WaitOne();
      // Thực hiện công việc
  }
  finally
  {
      semaphore.Release();
  }
  ```
- **Hiệu suất**: Sử dụng `SemaphoreSlim` thay cho `Semaphore` nếu không cần đồng bộ hóa giữa các tiến trình.
- **Giới hạn tối đa**: Đảm bảo `maximumCount` được thiết lập hợp lý để tránh vượt quá khả năng của tài nguyên.

### 7. Bài tập thực hành
1. **Bài tập 1**: Viết chương trình mô phỏng một bãi đỗ xe với sức chứa tối đa 5 xe. Sử dụng Semaphore để kiểm soát số lượng xe ra/vào bãi đỗ.
   - Yêu cầu:
     - Tạo 10 thread mô phỏng 10 xe muốn vào bãi đỗ.
     - Mỗi xe ở trong bãi đỗ trong khoảng thời gian ngẫu nhiên (1-3 giây).
     - In thông báo khi xe vào và rời bãi đỗ.
2. **Bài tập 2**: Sử dụng SemaphoreSlim để giới hạn số lượng yêu cầu HTTP đồng thời gửi đến một API (giả lập bằng `HttpClient`).
   - Yêu cầu:
     - Tạo 10 task gửi yêu cầu HTTP.
     - Giới hạn tối đa 3 yêu cầu đồng thời.
     - In kết quả trả về từ API.

### 8. Tài liệu tham khảo
- [Semaphore Class - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/api/system.threading.semaphore)
- [SemaphoreSlim Class - Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/api/system.threading.semaphoreslim)
- Video: [Học lập trình .NET - Phần 32 - Sử dụng Semaphore](https://www.youtube.com/watch?v=gh4TrZTJIfA)