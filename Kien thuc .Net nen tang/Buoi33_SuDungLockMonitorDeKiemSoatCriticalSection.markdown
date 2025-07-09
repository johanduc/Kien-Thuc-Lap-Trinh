# Buổi 33: Sử dụng lock, Monitor để kiểm soát các critical section

## 1. Giới thiệu về Multithreading và Critical Section
- **Multithreading**: Là khả năng của một chương trình thực hiện nhiều tác vụ đồng thời bằng cách sử dụng các luồng (threads). Mỗi luồng chạy độc lập nhưng có thể chia sẻ tài nguyên chung (như biến, bộ nhớ).
- **Critical Section**: Là đoạn mã mà tại một thời điểm chỉ một luồng được phép thực thi để tránh xung đột (race condition) khi nhiều luồng truy cập vào tài nguyên chung.
- **Vấn đề race condition**: Xảy ra khi nhiều luồng cùng truy cập và sửa đổi một tài nguyên chung, dẫn đến kết quả không mong muốn hoặc không xác định.
- **Giải pháp**: Sử dụng các cơ chế đồng bộ hóa như `lock` hoặc `Monitor` để kiểm soát truy cập vào critical section.

## 2. Cơ chế `lock` trong C#
- **Cú pháp**:
  ```csharp
  lock (object) {
      // Critical section
  }
  ```
- **Cách hoạt động**:
  - `lock` đảm bảo rằng chỉ một luồng được phép thực thi đoạn mã trong khối `lock` tại một thời điểm.
  - Đối tượng được sử dụng trong `lock` (lock object) phải là một tham chiếu (reference type), không nên là giá trị `null` hoặc kiểu giá trị (value type).
  - Thường sử dụng một đối tượng riêng (ví dụ: `private static readonly object _lock = new object();`) làm lock object để tránh nhầm lẫn.
- **Lưu ý**:
  - Không sử dụng các đối tượng như `this`, `typeof(Class)`, hoặc chuỗi (`string`) làm lock object vì chúng có thể được sử dụng ở nơi khác, dẫn đến lỗi hoặc deadlock.
  - `lock` là một cách đơn giản hóa của `Monitor`, giúp mã dễ đọc hơn.

## 3. Cơ chế `Monitor` trong C#
- **Cú pháp**:
  ```csharp
  Monitor.Enter(object);
  try {
      // Critical section
  }
  finally {
      Monitor.Exit(object);
  }
  ```
- **Cách hoạt động**:
  - `Monitor.Enter`: Khóa đối tượng, ngăn các luồng khác truy cập vào critical section.
  - `Monitor.Exit`: Giải phóng khóa để các luồng khác có thể truy cập.
  - Sử dụng `try-finally` để đảm bảo `Monitor.Exit` luôn được gọi, ngay cả khi có ngoại lệ xảy ra.
- **Ưu điểm**:
  - Linh hoạt hơn `lock` vì có thể sử dụng các phương thức bổ sung như `Monitor.Wait`, `Monitor.Pulse`, `Monitor.PulseAll` để quản lý luồng phức tạp hơn.
- **Nhược điểm**:
  - Cần quản lý thủ công `Monitor.Exit`, dễ dẫn đến lỗi nếu quên gọi.

## 4. So sánh `lock` và `Monitor`
| Tiêu chí            | `lock`                              | `Monitor`                          |
|---------------------|-------------------------------------|------------------------------------|
| **Cú pháp**         | Đơn giản, dễ sử dụng                | Phức tạp hơn, cần `try-finally`    |
| **Tính năng**       | Chỉ khóa cơ bản                    | Hỗ trợ `Wait`, `Pulse`, `PulseAll` |
| **Khả năng lỗi**    | Ít xảy ra lỗi                      | Dễ lỗi nếu quên `Exit`            |
| **Hiệu suất**       | Tương đương                        | Tương đương                       |

- **`lock` là lựa chọn ưu tiên** khi chỉ cần khóa đơn giản để bảo vệ critical section.
- **`Monitor` phù hợp** khi cần các cơ chế đồng bộ hóa phức tạp hơn (ví dụ: chờ luồng, thông báo giữa các luồng).

## 5. Ví dụ minh họa
### 5.1. Ví dụ 1: Race condition khi không sử dụng `lock`
- **Mô tả**: Một biến `counter` được tăng bởi nhiều luồng, dẫn đến kết quả không chính xác do race condition.
- **Mã ví dụ**:
  ```csharp
  class Program
  {
      static int counter = 0;

      static void Main(string[] args)
      {
          Thread[] threads = new Thread[5];
          for (int i = 0; i < 5; i++)
          {
              threads[i] = new Thread(() =>
              {
                  for (int j = 0; j < 1000; j++)
                  {
                      counter++; // Race condition xảy ra ở đây
                  }
              });
              threads[i].Start();
          }

          for (int i = 0; i < 5; i++)
          {
              threads[i].Join();
          }

          Console.WriteLine($"Counter: {counter}"); // Kết quả không chắc chắn, thường nhỏ hơn 5000
      }
  }
  ```
- **Kết quả**: Do nhiều luồng cùng tăng `counter` mà không có đồng bộ hóa, giá trị `counter` thường không đạt được 5000 như mong đợi.

### 5.2. Ví dụ 2: Sử dụng `lock` để khắc phục race condition
- **Mô tả**: Sử dụng `lock` để đảm bảo chỉ một luồng được tăng `counter` tại một thời điểm.
- **Mã ví dụ**:
  ```csharp
  class Program
  {
      static int counter = 0;
      static readonly object _lock = new object();

      static void Main(string[] args)
      {
          Thread[] threads = new Thread[5];
          for (int i = 0; i < 5; i++)
          {
              threads[i] = new Thread(() =>
              {
                  for (int j = 0; j < 1000; j++)
                  {
                      lock (_lock)
                      {
                          counter++;
                      }
                  }
              });
<xaiArtifact artifact_id="53cb8d90-7f9b-4aa9-afe3-ea9b1674505f" artifact_version_id="e88d7e7a-2c00-488c-83f9-0c78bd7ecebd" title="Buoi33_SuDungLockMonitorDeKiemSoatCriticalSection.md" contentType="text/markdown">

# Buổi 33: Sử dụng lock, Monitor để kiểm soát các critical section

## 1. Giới thiệu về Multithreading và Critical Section
- **Multithreading**: Là khả năng của một chương trình thực hiện nhiều tác vụ đồng thời bằng cách sử dụng các luồng (threads). Mỗi luồng chạy độc lập nhưng có thể chia sẻ tài nguyên chung (như biến, bộ nhớ).
- **Critical Section**: Là đoạn mã mà tại một thời điểm chỉ một luồng được phép thực thi để tránh xung đột (race condition) khi nhiều luồng truy cập vào tài nguyên chung.
- **Vấn đề race condition**: Xảy ra khi nhiều luồng cùng truy cập và sửa đổi một tài nguyên chung, dẫn đến kết quả không mong muốn hoặc không xác định.
- **Giải pháp**: Sử dụng các cơ chế đồng bộ hóa như `lock` hoặc `Monitor` để kiểm soát truy cập vào critical section.

## 2. Cơ chế `lock` trong C#
- **Cú pháp**:
  ```csharp
  lock (object) {
      // Critical section
  }
  ```
- **Cách hoạt động**:
  - `lock` đảm bảo rằng chỉ một luồng được phép thực thi đoạn mã trong khối `lock` tại một thời điểm.
  - Đối tượng được sử dụng trong `lock` (lock object) phải là một tham chiếu (reference type), không nên là giá trị `null` hoặc kiểu giá trị (value type).
  - Thường sử dụng một đối tượng riêng (ví dụ: `private static readonly object _lock = new object();`) làm lock object để tránh nhầm lẫn.
- **Lưu ý**:
  - Không sử dụng các đối tượng như `this`, `typeof(Class)`, hoặc chuỗi (`string`) làm lock object vì chúng có thể được sử dụng ở nơi khác, dẫn đến lỗi hoặc deadlock.
  - `lock` là một cách đơn giản hóa của `Monitor`, giúp mã dễ đọc hơn.

## 3. Cơ chế `Monitor` trong C#
- **Cú pháp**:
  ```csharp
  Monitor.Enter(object);
  try {
      // Critical section
  }
  finally {
      Monitor.Exit(object);
  }
  ```
- **Cách hoạt động**:
  - `Monitor.Enter`: Khóa đối tượng, ngăn các luồng khác truy cập vào critical section.
  - `Monitor.Exit`: Giải phóng khóa để các luồng khác có thể truy cập.
  - Sử dụng `try-finally` để đảm bảo `Monitor.Exit` luôn được gọi, ngay cả khi có ngoại lệ xảy ra.
- **Ưu điểm**:
  - Linh hoạt hơn `lock` vì có thể sử dụng các phương thức bổ sung như `Monitor.Wait`, `Monitor.Pulse`, `Monitor.PulseAll` để quản lý luồng phức tạp hơn.
- **Nhược điểm**:
  - Cần quản lý thủ công `Monitor.Exit`, dễ dẫn đến lỗi nếu quên gọi.

## 4. So sánh `lock` và `Monitor`
| Tiêu chí            | `lock`                              | `Monitor`                          |
|---------------------|-------------------------------------|------------------------------------|
| **Cú pháp**         | Đơn giản, dễ sử dụng                | Phức tạp hơn, cần `try-finally`    |
| **Tính năng**       | Chỉ khóa cơ bản                    | Hỗ trợ `Wait`, `Pulse`, `PulseAll` |
| **Khả năng lỗi**    | Ít xảy ra lỗi                      | Dễ lỗi nếu quên `Exit`            |
| **Hiệu suất**       | Tương đương                        | Tương đương                       |

- **`lock` là lựa chọn ưu tiên** khi chỉ cần khóa đơn giản để bảo vệ critical section.
- **`Monitor` phù hợp** khi cần các cơ chế đồng bộ hóa phức tạp hơn (ví dụ: chờ luồng, thông báo giữa các luồng).

## 5. Ví dụ minh họa
### 5.1. Ví dụ 1: Race condition khi không sử dụng `lock`
- **Mô tả**: Một biến `counter` được tăng bởi nhiều luồng, dẫn đến kết quả không chính xác do race condition.
- **Mã ví dụ**:
  ```csharp
  class Program
  {
      static int counter = 0;

      static void Main(string[] args)
      {
          Thread[] threads = new Thread[5];
          for (int i = 0; i < 5; i++)
          {
              threads[i] = new Thread(() =>
              {
                  for (int j = 0; j < 1000; j++)
                  {
                      counter++; // Race condition xảy ra ở đây
                  }
              });
              threads[i].Start();
          }

          for (int i = 0; i < 5; i++)
          {
              Threads[i].Join();
          }

          Console.WriteLine($"Counter: {counter}"); // Kết quả không chắc chắn, thường nhỏ hơn 5000
      }
  }
  ```
- **Kết quả**: Do nhiều luồng cùng tăng `counter` mà không có đồng bộ hóa, giá trị `counter` thường không đạt được 5000 như mong đợi.

### 5.2. Ví dụ 2: Sử dụng `lock` để khắc phục race condition
- **Mô tả**: Sử dụng `lock` để đảm bảo chỉ một luồng được tăng `counter` tại một thời điểm.
- **Mã ví dụ**:
  ```csharp
  class Program
  {
      static int counter = 0;
      static readonly object _lock = new object();

      static void Main(string[] args)
      {
          Thread[] threads = new Thread[5];
          for (int i = 0; i < 5; i++)
          {
              threads[i] = new Thread(() =>
              {
                  for (int j = 0; j < 1000; j++)
                  {
                      lock (_lock)
                      {
                          counter++;
                      }
                  }
              });
              threads[i].Start();
          }

          for (int i = 0; i < 5; i++)
          {
              threads[i].Join();
          }

          Console.WriteLine($"Counter: {counter}"); // Kết quả đúng: 5000
      }
  }
  ```
- **Kết quả**: Với `lock`, giá trị `counter` luôn đạt đúng 5000 vì các luồng được đồng bộ hóa.

### 5.3. Ví dụ 3: Sử dụng `Monitor` để kiểm soát critical section
- **Mô tả**: Sử dụng `Monitor` thay vì `lock` để tăng `counter`.
- **Mã ví dụ**:
  ```csharp
  class Program
  {
      static int counter = 0;
      static readonly object _lock = new object();

      static void Main(string[] args)
      {
          Thread[] threads = new Thread[5];
          for (int i = 0; i < 5; i++)
          {
              threads[i] = new Thread(() =>
              {
                  for (int j = 0; j < 1000; j++)
                  {
                      Monitor.Enter(_lock);
                      try
                      {
                          counter++;
                      }
                      finally
                      {
                          Monitor.Exit(_lock);
                      }
                  }
              });
              threads[i].Start();
          }

          for (int i = 0; i < 5; i++)
          {
              threads[i].Join();
          }

          Console.WriteLine($"Counter: {counter}"); // Kết quả đúng: 5000
      }
  }
  ```
- **Kết quả**: Tương tự như `lock`, `Monitor` đảm bảo kết quả đúng là 5000.

### 5.4. Ví dụ 4: Sử dụng `Monitor.Wait` và `Monitor.Pulse`
- **Mô tả**: Sử dụng `Monitor.Wait` và `Monitor.Pulse` để quản lý thứ tự thực thi của các luồng.
- **Mã ví dụ**:
  ```csharp
  class Program
  {
      static readonly object _lock = new object();
      static bool condition = false;

      static void Main(string[] args)
      {
          Thread producer = new Thread(Producer);
          Thread consumer = new Thread(Consumer);

          producer.Start();
          consumer.Start();

          producer.Join();
          consumer.Join();
      }

      static void Producer()
      {
          lock (_lock)
          {
              Console.WriteLine("Producer: Producing data...");
              Thread.Sleep(2000); // Giả lập thời gian xử lý
              condition = true;
              Monitor.Pulse(_lock); // Thông báo cho consumer
              Console.WriteLine("Producer: Data produced.");
          }
      }

      static void Consumer()
      {
          lock (_lock)
          {
              while (!condition)
              {
                  Console.WriteLine("Consumer: Waiting for data...");
                  Monitor.Wait(_lock); // Chờ producer
              }
              Console.WriteLine("Consumer: Consuming data...");
          }
      }
  }
  ```
- **Kết quả**:
  ```
  Consumer: Waiting for data...
  Producer: Producing data...
  Producer: Data produced.
  Consumer: Consuming data...
  ```
- **Giải thích**:
  - `Monitor.Wait`: Tạm dừng luồng và giải phóng khóa, chờ thông báo từ `Monitor.Pulse`.
  - `Monitor.Pulse`: Thông báo cho một luồng đang chờ để tiếp tục.

## 6. Lưu ý khi sử dụng `lock` và `Monitor`
- **Deadlock**: Có thể xảy ra nếu các luồng khóa lẫn nhau (ví dụ: luồng A chờ khóa của luồng B và ngược lại). Để tránh:
  - Sử dụng thứ tự khóa cố định.
  - Tránh lồng nhiều `lock` hoặc `Monitor` phức tạp.
- **Hiệu suất**: Khóa quá nhiều hoặc khóa lâu có thể làm giảm hiệu suất. Chỉ khóa trong phạm vi cần thiết.
- **Sử dụng đúng lock object**:
  - Luôn sử dụng một đối tượng riêng, bất biến (immutable) làm lock object.
  - Tránh sử dụng `string` hoặc các đối tượng công khai vì chúng có thể bị khóa ở nơi khác.
- **Thay thế khác**:
  - Ngoài `lock` và `Monitor`, .NET cung cấp các cơ chế đồng bộ khác như `Mutex`, `Semaphore`, `Interlocked`, hoặc các lớp trong `System.Threading` (ví dụ: `ConcurrentDictionary`, `ConcurrentQueue`).

## 7. Bài tập thực hành
1. **Bài tập 1**: Viết chương trình sử dụng 3 luồng để tăng một biến `total` lên 3000 (mỗi luồng tăng 1000 lần). So sánh kết quả khi có và không có `lock`.
2. **Bài tập 2**: Sử dụng `Monitor` để viết chương trình mô phỏng một hàng đợi (queue) có giới hạn 5 phần tử. Một luồng producer thêm phần tử, một luồng consumer lấy phần tử. Sử dụng `Monitor.Wait` và `Monitor.Pulse` để đồng bộ.
3. **Bài tập 3**: Tìm hiểu về `Interlocked.Increment` và so sánh với `lock` trong việc tăng giá trị biến `counter`. Khi nào nên sử dụng `Interlocked` thay vì `lock`?

## 8. Kết luận
- `lock` và `Monitor` là hai cơ chế quan trọng trong .NET để kiểm soát critical section và tránh race condition.
- `lock` phù hợp cho các tình huống đơn giản, trong khi `Monitor` cung cấp tính linh hoạt hơn cho các kịch bản phức tạp.
- Hiểu rõ cách sử dụng và các vấn đề tiềm ẩn (như deadlock) giúp viết mã multithreading an toàn và hiệu quả.