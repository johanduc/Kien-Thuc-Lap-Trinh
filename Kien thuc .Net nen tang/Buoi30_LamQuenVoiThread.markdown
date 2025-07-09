# Buổi 30: Làm quen với Thread

## Mục tiêu

- Hiểu khái niệm Thread (luồng) trong lập trình.
- Nắm cách tạo và sử dụng Thread trong .NET.
- Hiểu các vấn đề liên quan đến Thread như đồng bộ hóa và deadlock.
- Thực hành các ví dụ cơ bản về Thread.

## Nội dung chính

### 1. Thread là gì?

- **Thread** (luồng) là đơn vị nhỏ nhất của quá trình xử lý mà hệ điều hành có thể thực thi.
- Một chương trình có thể có nhiều thread chạy song song để thực hiện các tác vụ khác nhau.
- Trong .NET, Thread được quản lý bởi lớp `System.Threading.Thread`.

### 2. Tại sao cần Thread?

- Tăng hiệu suất: Cho phép thực hiện nhiều tác vụ cùng lúc.
- Cải thiện trải nghiệm người dùng: Các tác vụ nặng (như tải dữ liệu, xử lý file) không làm "đơ" giao diện.
- Phù hợp cho các ứng dụng đa nhiệm.

### 3. Cách tạo Thread trong .NET

- Sử dụng lớp `Thread` từ namespace `System.Threading`.
- Cú pháp cơ bản:

```csharp
Thread thread = new Thread(new ThreadStart(TenHam));
thread.Start();
```

- Trong đó:
  - `ThreadStart`: Delegate chỉ định phương thức mà thread sẽ thực thi.
  - `TenHam`: Phương thức được gọi khi thread chạy.

### 4. Ví dụ cơ bản về Thread

#### Ví dụ 1: Tạo một Thread đơn giản

```csharp
using System;
using System.Threading;

class Program
{
    static void Main(string[] args)
    {
        Thread thread = new Thread(new ThreadStart(PrintHello));
        thread.Start();
        Console.WriteLine("Main thread is running");
    }

    static void PrintHello()
    {
        for (int i = 0; i < 5; i++)
        {
            Console.WriteLine("Hello from thread!");
            Thread.Sleep(1000); // Tạm dừng 1 giây
        }
    }
}
```

**Giải thích**:

- Tạo một thread mới với phương thức `PrintHello`.
- Gọi `thread.Start()` để bắt đầu thread.
- `Thread.Sleep(1000)`: Tạm dừng thread 1 giây sau mỗi lần in.

**Kết quả**:

- Main thread và thread mới chạy song song.
- Xuất ra:
  ```
  Main thread is running
  Hello from thread!
  Hello from thread!
  ...
  ```

#### Ví dụ 2: Thread với tham số

- Sử dụng `ParameterizedThreadStart` để truyền tham số cho thread.

```csharp
using System;
using System.Threading;

class Program
{
    static void Main(string[] args)
    {
        Thread thread = new Thread(new ParameterizedThreadStart(PrintMessage));
        thread.Start("Xin chào từ thread!");
    }

    static void PrintMessage(object message)
    {
        for (int i = 0; i < 5; i++)
        {
            Console.WriteLine(message);
            Thread.Sleep(1000);
        }
    }
}
```

**Giải thích**:

- `ParameterizedThreadStart`: Delegate cho phép truyền tham số vào phương thức.
- `thread.Start("Xin chào từ thread!")`: Truyền chuỗi vào thread.

### 5. Các phương thức và thuộc tính quan trọng của Thread

- **Phương thức**:
  - `Start()`: Bắt đầu thread.
  - `Sleep(int milliseconds)`: Tạm dừng thread trong số mili giây chỉ định.
  - `Join()`: Chờ thread hoàn thành trước khi tiếp tục.
  - `Abort()`: Kết thúc thread (không khuyến khích, dễ gây lỗi).
- **Thuộc tính**:
  - `IsAlive`: Kiểm tra thread còn hoạt động không.
  - `Name`: Đặt tên cho thread để dễ theo dõi.
  - `Priority`: Độ ưu tiên của thread (ThreadPriority.Lowest, Normal, Highest...).

### 6. Vấn đề đồng bộ hóa (Synchronization)

- Khi nhiều thread truy cập cùng tài nguyên (ví dụ: biến chung), có thể xảy ra xung đột.
- **Lock**: Sử dụng từ khóa `lock` để đảm bảo chỉ một thread truy cập tài nguyên tại một thời điểm.

#### Ví dụ 3: Sử dụng Lock

```csharp
using System;
using System.Threading;

class Program
{
    static int counter = 0;
    static object lockObject = new object();

    static void Main(string[] args)
    {
        Thread t1 = new Thread(IncrementCounter);
        Thread t2 = new Thread(IncrementCounter);
        t1.Start();
        t2.Start();
        t1.Join();
        t2.Join();
        Console.WriteLine("Counter: " + counter);
    }

    static void IncrementCounter()
    {
        for (int i = 0; i < 1000; i++)
        {
            lock (lockObject)
            {
                counter++;
            }
        }
    }
}
```

**Giải thích**:

- `lock(lockObject)`: Đảm bảo chỉ một thread tăng `counter` tại một thời điểm.
- Nếu không có `lock`, giá trị `counter` có thể không chính xác do xung đột.

### 7. Deadlock

- **Deadlock**: Xảy ra khi hai hoặc nhiều thread chờ nhau, dẫn đến chương trình bị treo.
- Ví dụ: Thread A giữ khóa 1 và chờ khóa 2, Thread B giữ khóa 2 và chờ khóa 1.

#### Ví dụ 4: Minh họa Deadlock

```csharp
using System;
using System.Threading;

class Program
{
    static object lock1 = new object();
    static object lock2 = new object();

    static void Main(string[] args)
    {
        Thread t1 = new Thread(Thread1Work);
        Thread t2 = new Thread(Thread2Work);
        t1.Start();
        t2.Start();
        t1.Join();
        t2.Join();
        Console.WriteLine("Finished");
    }

    static void Thread1Work()
    {
        lock (lock1)
        {
            Console.WriteLine("Thread 1 locked lock1");
            Thread.Sleep(1000);
            lock (lock2)
            {
                Console.WriteLine("Thread 1 locked lock2");
            }
        }
    }

    static void Thread2Work()
    {
        lock (lock2)
        {
            Console.WriteLine("Thread 2 locked lock2");
            Thread.Sleep(1000);
            lock (lock1)
            {
                Console.WriteLine("Thread 2 locked lock1");
            }
        }
    }
}
```

**Giải thích**:

- Thread 1 giữ `lock1` và chờ `lock2`.
- Thread 2 giữ `lock2` và chờ `lock1`.
- Kết quả: Chương trình bị treo (deadlock).

**Cách tránh Deadlock**:

- Đảm bảo các thread khóa tài nguyên theo cùng một thứ tự.
- Sử dụng `Monitor.TryEnter` để thử khóa mà không chờ vô thời hạn.

### 8. Thread Pool

- .NET cung cấp `ThreadPool` để quản lý các thread hiệu quả hơn, tránh tạo quá nhiều thread thủ công.
- Ví dụ sử dụng `ThreadPool`:

```csharp
using System;
using System.Threading;

class Program
{
    static void Main(string[] args)
    {
        ThreadPool.QueueUserWorkItem(DoWork, "Task 1");
        ThreadPool.QueueUserWorkItem(DoWork, "Task 2");
        Console.ReadLine();
    }

    static void DoWork(object state)
    {
        Console.WriteLine($"Working on {state}");
        Thread.Sleep(1000);
        Console.WriteLine($"{state} completed");
    }
}
```

**Giải thích**:

- `ThreadPool.QueueUserWorkItem`: Đưa công việc vào ThreadPool để thực thi.
- ThreadPool tự quản lý các thread, tái sử dụng khi cần.

### 9. Bài tập thực hành

1. **Bài tập 1**: Viết chương trình sử dụng 2 thread để in lần lượt các số chẵn và lẻ từ 1 đến 100.
2. **Bài tập 2**: Viết chương trình sử dụng ThreadPool để thực hiện 5 tác vụ in chuỗi ngẫu nhiên.
3. **Bài tập 3**: Tạo một chương trình mô phỏng 2 thread cạnh tranh tài nguyên, sử dụng `lock` để tránh xung đột.

#### Giải bài tập 1

```csharp
using System;
using System.Threading;

class Program
{
    static void Main(string[] args)
    {
        Thread evenThread = new Thread(PrintEvenNumbers);
        Thread oddThread = new Thread(PrintOddNumbers);
        evenThread.Start();
        oddThread.Start();
        evenThread.Join();
        oddThread.Join();
        Console.WriteLine("Done");
    }

    static void PrintEvenNumbers()
    {
        for (int i = 2; i <= 100; i += 2)
        {
            Console.WriteLine($"Even: {i}");
            Thread.Sleep(100);
        }
    }

    static void PrintOddNumbers()
    {
        for (int i = 1; i <= 100; i += 2)
        {
            Console.WriteLine($"Odd: {i}");
            Thread.Sleep(100);
        }
    }
}
```

**Kết quả**:

- In xen kẽ các số chẵn và lẻ từ 1 đến 100.

## Tổng kết

- Thread giúp thực hiện các tác vụ song song, tăng hiệu suất.
- Cần cẩn thận với đồng bộ hóa để tránh xung đột tài nguyên.
- ThreadPool là cách hiệu quả để quản lý nhiều thread.
- Deadlock là vấn đề cần chú ý và có thể tránh bằng cách thiết kế khóa hợp lý.
