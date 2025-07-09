# Buổi 9 - Biểu thức Lambda

## 1. Giới thiệu về Biểu thức Lambda

- **Biểu thức Lambda** (Lambda Expression) là một cách viết ngắn gọn để định nghĩa một phương thức vô danh (anonymous method) trong C#.
- Cú pháp cơ bản: `(tham_số) => biểu_thức`
  - Dấu `=>` được gọi là toán tử Lambda.
  - Bên trái: Danh sách tham số (có thể có hoặc không).
  - Bên phải: Biểu thức hoặc khối lệnh thực thi.
- Lambda thường được sử dụng với **delegate**, **LINQ**, hoặc các phương thức nhận tham số là một delegate.

### Ví dụ cơ bản:

```csharp
// Định nghĩa một delegate
delegate int MyDelegate(int x);

// Sử dụng Lambda Expression
MyDelegate square = x => x * x;
Console.WriteLine(square(5)); // Kết quả: 25
```

## 2. Cú pháp chi tiết của Biểu thức Lambda

- **Không tham số**:
  ```csharp
  () => Console.WriteLine("Hello Lambda!");
  ```
- **Một tham số**:
  ```csharp
  x => x * x
  ```
- **Nhiều tham số**:
  ```csharp
  (x, y) => x + y
  ```
- **Khối lệnh** (khi cần nhiều dòng code):
  ```csharp
  (x, y) =>
  {
      int sum = x + y;
      return sum;
  }
  ```

## 3. Ứng dụng của Biểu thức Lambda

### 3.1. Sử dụng với Delegate

- Lambda thay thế cho phương thức vô danh hoặc phương thức thông thường khi gán cho delegate.
- Ví dụ:
  ```csharp
  delegate void Display(string message);
  Display display = message => Console.WriteLine(message);
  display("Hello from Lambda!"); // Kết quả: Hello from Lambda!
  ```

### 3.2. Sử dụng với LINQ

- Lambda thường được sử dụng trong các phương thức LINQ như `Where`, `Select`, `OrderBy`, v.v.
- Ví dụ:
  ```csharp
  List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6 };
  var evenNumbers = numbers.Where(x => x % 2 == 0);
  foreach (var num in evenNumbers)
  {
      Console.WriteLine(num); // Kết quả: 2, 4, 6
  }
  ```

### 3.3. Sử dụng với Action và Func

- **Action**: Delegate không trả về giá trị.
- **Func**: Delegate có giá trị trả về.
- Ví dụ:

  ```csharp
  // Action
  Action<string> greet = name => Console.WriteLine($"Hello {name}!");
  greet("John"); // Kết quả: Hello John!

  // Func
  Func<int, int, int> add = (a, b) => a + b;
  Console.WriteLine(add(3, 4)); // Kết quả: 7
  ```

## 4. Bài tập ví dụ trong video

### Bài tập 1: Lọc danh sách số nguyên

- Yêu cầu: Cho một danh sách số nguyên, sử dụng biểu thức Lambda để lọc ra các số lớn hơn 10.
- Code:
  ```csharp
  List<int> numbers = new List<int> { 5, 12, 8, 15, 3, 20 };
  var filteredNumbers = numbers.Where(x => x > 10);
  Console.WriteLine("Các số lớn hơn 10:");
  foreach (var num in filteredNumbers)
  {
      Console.WriteLine(num); // Kết quả: 12, 15, 20
  }
  ```

### Bài tập 2: Sắp xếp danh sách chuỗi

- Yêu cầu: Cho một danh sách các chuỗi, sử dụng biểu thức Lambda để sắp xếp theo độ dài chuỗi (tăng dần).
- Code:
  ```csharp
  List<string> words = new List<string> { "apple", "banana", "cat", "dog" };
  var sortedWords = words.OrderBy(x => x.Length);
  Console.WriteLine("Sắp xếp theo độ dài chuỗi:");
  foreach (var word in sortedWords)
  {
      Console.WriteLine(word); // Kết quả: cat, dog, apple, banana
  }
  ```

### Bài tập 3: Tìm kiếm trong danh sách đối tượng

- Yêu cầu: Cho một danh sách đối tượng `Person`, sử dụng biểu thức Lambda để tìm các người có tuổi lớn hơn 25.
- Code:

  ```csharp
  public class Person
  {
      public string Name { get; set; }
      public int Age { get; set; }
  }

  List<Person> people = new List<Person>
  {
      new Person { Name = "John", Age = 30 },
      new Person { Name = "Jane", Age = 22 },
      new Person { Name = "Bob", Age = 28 }
  };

  var olderThan25 = people.Where(p => p.Age > 25);
  Console.WriteLine("Những người trên 25 tuổi:");
  foreach (var person in olderThan25)
  {
      Console.WriteLine($"Tên: {person.Name}, Tuổi: {person.Age}");
      // Kết quả:
      // Tên: John, Tuổi: 30
      // Tên: Bob, Tuổi: 28
  }
  ```

## 5. Lưu ý khi sử dụng Biểu thức Lambda

- **Độ dài và tính rõ ràng**: Lambda giúp code ngắn gọn, nhưng nếu quá phức tạp, nên sử dụng phương thức thông thường để dễ đọc.
- **Hiệu suất**: Lambda không ảnh hưởng đáng kể đến hiệu suất, nhưng cần chú ý khi sử dụng trong các vòng lặp lớn.
- **Capture biến**: Lambda có thể truy cập các biến bên ngoài (closure), cần cẩn thận để tránh lỗi không mong muốn.
  - Ví dụ:
    ```csharp
    int factor = 2;
    Func<int, int> multiplier = x => x * factor;
    Console.WriteLine(multiplier(5)); // Kết quả: 10
    ```

## 6. Kết luận

- Biểu thức Lambda là một công cụ mạnh mẽ trong C#, giúp viết code ngắn gọn và linh hoạt.
- Ứng dụng phổ biến trong delegate, LINQ, và các thư viện như `Action`, `Func`.
- Cần sử dụng Lambda một cách hợp lý để đảm bảo code dễ đọc và dễ bảo trì.

## 7. Bài tập tự luyện

1. Sử dụng Lambda để lọc một danh sách chuỗi, chỉ giữ lại các chuỗi bắt đầu bằng chữ "A".
2. Viết một chương trình sử dụng Lambda để tính tổng các số lẻ trong một danh sách số nguyên.
3. Sử dụng Lambda để sắp xếp một danh sách đối tượng `Student` theo điểm số giảm dần.
