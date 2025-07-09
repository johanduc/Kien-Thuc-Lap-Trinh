# Buổi 31: Sử dụng EventWaitHandle để đồng bộ hoạt động nhiều thread

## 1. Tổng quan về đồng bộ hóa thread
- **Thread** (luồng) là các đơn vị thực thi nhỏ nhất trong một tiến trình. Khi nhiều thread chạy đồng thời, cần đồng bộ hóa để tránh xung đột tài nguyên hoặc đảm bảo thứ tự thực thi.
- **EventWaitHandle** là một cơ chế đồng bộ hóa trong .NET, được sử dụng để điều phối hoạt động giữa các thread, cho phép một thread chờ một sự kiện từ thread khác.

### Mục đích của EventWaitHandle
- Cho phép một thread tạm dừng (wait) cho đến khi nhận được tín hiệu (signal) từ thread khác.
- Được sử dụng khi cần điều khiển thứ tự thực thi hoặc đồng bộ hóa các tác vụაშ

### Các loại EventWaitHandle
- **Auto-reset**: Tự động reset trạng thái sau khi được signal.
- **Manual-reset**: Yêu cầu gọi `Reset()` để trở về trạng thái không signal.

### Các phương thức chính
- `Set()`: Gửi tín hiệu để thông báo thread đang chờ.
- `WaitOne()`: Làm thread hiện tại chờ tín hiệu từ `EventWaitHandle`.
- `Reset()`: Đặt lại trạng thái không signal (chỉ áp dụng cho Manual-reset).

## 2. Các ví dụ minh họa

### Ví dụ 1: Sử dụng AutoResetEvent
- **Mô tả**: Một thread chính chờ tín hiệu từ một thread khác thông qua `AutoResetEvent`.
- **Code ví dụ**:

```csharp
using System;
using System.Threading;

class Program
{
    // Khởi tạo AutoResetEvent với trạng thái ban đầu là false (không signal)
    static AutoResetEvent autoResetEvent = new AutoResetEvent(false);

    static void Main(string[] args)
    {
        // Tạo thread phụ
        Thread workerThread = new Thread(WorkerMethod);
        workerThread.Start();

        Console.WriteLine("Main thread đang chờ tín hiệu...");
        autoResetEvent.WaitOne(); // Main thread chờ tín hiệu
        Console.WriteLine("Main thread nhận được tín hiệu!");

        Console.ReadLine();
    }

    static void WorkerMethod()
    {
        Console.WriteLine("Worker thread đang thực hiện công việc...");
        Thread.Sleep(2000); // Giả lập công việc mất 2 giây
        Console.WriteLine("Worker thread hoàn thành công việc!");
        autoResetEvent.Set(); // Gửi tín hiệu cho main thread
    }
}
```

- **Giải thích**:
  - `AutoResetEvent` được khởi tạo với trạng thái `false` (không signal).
  - Thread chính gọi `WaitOne()` để chờ tín hiệu.
  - Thread phụ (`WorkerMethod`) thực hiện công việc, sau đó gọi `Set()` để gửi tín hiệu.
  - Khi tín hiệu được gửi, thread chính tiếp tục thực thi.
  - `AutoResetEvent` tự động reset về trạng thái `false` sau khi tín hiệu được nhận.

### Ví dụ 2: Sử dụng ManualResetEvent
- **Mô tả**: Tương tự ví dụ 1, nhưng sử dụng `ManualResetEvent` để kiểm soát việc reset trạng thái thủ công.
- **Code ví dụ**:

```csharp
using System;
using System.Threading;

class Program
{
    // Khởi tạo ManualResetEvent với trạng thái ban đầu là false
    static ManualResetEvent manualResetEvent = new ManualResetEvent(false);

    static void Main(string[] args)
    {
        Thread workerThread = new Thread(WorkerMethod);
        workerThread.Start();

        Console.WriteLine("Main thread đang chờ tín hiệu...");
        manualResetEvent.WaitOne(); // Main thread chờ tín hiệu
        Console.WriteLine("Main thread nhận được tín hiệu!");
        manualResetEvent.Reset(); // Reset trạng thái thủ công
        Console.WriteLine("ManualResetEvent đã được reset.");

        Console.ReadLine();
    }

    static void WorkerMethod()
    {
        Console.WriteLine("Worker thread đang thực hiện công việc...");
        Thread.Sleep(2000); // Giả lập công việc mất 2 giây
        Console.WriteLine("Worker thread hoàn thành công việc!");
        manualResetEvent.Set(); // Gửi tín hiệu cho main thread
    }
}
```

- **Giải thích**:
  - Khác với `AutoResetEvent`, `ManualResetEvent` không tự động reset trạng thái sau khi tín hiệu được nhận.
  - Cần gọi `Reset()` thủ công để đặt lại trạng thái `false`.
  - Điều này hữu ích khi muốn nhiều thread chờ cùng một tín hiệu mà không cần gửi tín hiệu lại.

### Ví dụ 3: Đồng bộ hóa nhiều thread
- **Mô tả**: Một thread chính chờ hai thread phụ hoàn thành công việc bằng cách sử dụng nhiều `AutoResetEvent`.
- **Code ví dụ**:

```csharp
using System;
using System.Threading;

class Program
{
    static AutoResetEvent event1 = new AutoResetEvent(false);
    static AutoResetEvent event2 = new AutoResetEvent(false);

    static void Main(string[] args)
    {
        Thread worker1 = new Thread(WorkerMethod1);
        Thread worker2 = new Thread(WorkerMethod2);

        worker1.Start();
        worker2.Start();

        Console.WriteLine("Main thread đang chờ cả hai thread hoàn thành...");
        WaitHandle.WaitAll(new WaitHandle[] { event1, event2 }); // Chờ cả hai tín hiệu
        Console.WriteLine("Cả hai thread đã hoàn thành!");

        Console.ReadLine();
    }

    static void WorkerMethod1()
    {
        Console.WriteLine("Worker 1 đang thực hiện...");
        Thread.Sleep(2000); // Giả lập công việc
        Console.WriteLine("Worker 1 hoàn thành!");
        event1.Set(); // Gửi tín hiệu 1
    }

    static void WorkerMethod2()
    {
        Console.WriteLine("Worker 2 đang thực hiện...");
        Thread.Sleep(3000); // Giả lập công việc lâu hơn
        Console.WriteLine("Worker 2 hoàn thành!");
        event2.Set(); // Gửi tín hiệu 2
    }
}
```

- **Giải thích**:
  - Sử dụng `WaitHandle.WaitAll` để chờ tất cả các `EventWaitHandle` trong mảng được signal.
  - Thread chính chỉ tiếp tục khi cả hai thread phụ gọi `Set()`.
  - `AutoResetEvent` được sử dụng để đảm bảo mỗi thread phụ chỉ gửi tín hiệu một lần.

## 3. So sánh AutoResetEvent và ManualResetEvent
| **Tiêu chí**           | **AutoResetEvent**                          | **ManualResetEvent**                        |
|------------------------|---------------------------------------------|---------------------------------------------|
| **Reset tự động**      | Tự động reset sau khi tín hiệu được nhận    | Cần gọi `Reset()` thủ công                  |
| **Sử dụng**            | Phù hợp cho các tình huống 1-1 (một thread chờ một tín hiệu) | Phù hợp khi nhiều thread cần nhận cùng tín hiệu |
| **Độ phức tạp**        | Đơn giản hơn                                | Linh hoạt hơn, nhưng cần quản lý trạng thái |

## 4. Ứng dụng thực tế
- **Hệ thống hàng đợi (Queue System)**: Sử dụng `EventWaitHandle` để điều phối các tác vụ giữa producer và consumer.
- **Quy trình làm việc tuần tự**: Đảm bảo các bước trong quy trình được thực hiện theo đúng thứ tự.
- **Tích hợp với các cơ chế đồng bộ khác**: Kết hợp với `Mutex`, `Semaphore` để quản lý tài nguyên phức tạp.

## 5. Lưu ý khi sử dụng
- **Deadlock**: Tránh để các thread chờ lẫn nhau mà không gửi tín hiệu, dẫn đến kẹt thread.
- **Hiệu suất**: `EventWaitHandle` có thể gây chậm nếu sử dụng không đúng cách.
- **Tài nguyên**: Đảm bảo các đối tượng `EventWaitHandle` được giải phóng (dispose) khi không sử dụng.

## 6. Bài tập thực hành
1. **Bài tập 1**: Viết chương trình sử dụng `AutoResetEvent` để mô phỏng một máy in chỉ in khi nhận được tín hiệu từ thread chính.
2. **Bài tập 2**: Sử dụng `ManualResetEvent` để tạo một hệ thống cho phép nhiều thread cùng chờ một tín hiệu khởi động từ thread chính.
3. **Bài tập 3**: Kết hợp `AutoResetEvent` và `Semaphore` để tạo một hệ thống hàng đợi có giới hạn tài nguyên.