# Học lập trình .NET - Phần 4: Sử dụng các cấu trúc điều khiển

## Mục tiêu

- Hiểu và sử dụng các **cấu trúc điều khiển** trong C# để điều hướng luồng thực thi chương trình.
- Nắm vững các cấu trúc:
  - Câu lệnh điều kiện: `if`, `if-else`, `switch-case`.
  - Vòng lặp: `for`, `while`, `do-while`, `foreach`.
  - Câu lệnh nhảy: `break`, `continue`, `return`.

## 1. Câu lệnh điều kiện

Câu lệnh điều kiện cho phép chương trình thực thi các khối mã khác nhau dựa trên điều kiện logic.

### 1.1. Câu lệnh `if`

- **Cú pháp**:
  ```csharp
  if (điều_kiện)
  {
      // Khối mã thực thi nếu điều kiện đúng
  }
  ```
- **Ví dụ**: Kiểm tra số chẵn/lẻ.
  ```csharp
  int number = 10;
  if (number % 2 == 0)
  {
      Console.WriteLine("Số {0} là số chẵn", number);
  }
  ```

### 1.2. Câu lệnh `if-else`

- **Cú pháp**:
  ```csharp
  if (điều_kiện)
  {
      // Khối mã thực thi nếu điều kiện đúng
  }
  else
  {
      // Khối mã thực thi nếu điều kiện sai
  }
  ```
- **Ví dụ**: Kiểm tra số dương hay âm.
  ```csharp
  int number = -5;
  if (number > 0)
  {
      Console.WriteLine("Số {0} là số dương", number);
  }
  else
  {
      Console.WriteLine("Số {0} là số âm hoặc bằng 0", number);
  }
  ```

### 1.3. Câu lệnh `if-else` lồng nhau

- Dùng khi có nhiều điều kiện cần kiểm tra.
- **Ví dụ**: Xếp loại học sinh dựa trên điểm.
  ```csharp
  double score = 8.5;
  if (score >= 8.0)
  {
      Console.WriteLine("Xếp loại Giỏi");
  }
  else if (score >= 6.5)
  {
      Console.WriteLine("Xếp loại Khá");
  }
  else if (score >= 5.0)
  {
      Console.WriteLine("Xếp loại Trung bình");
  }
  else
  {
      Console.WriteLine("Xếp loại Yếu");
  }
  ```

### 1.4. Câu lệnh `switch-case`

- Dùng để kiểm tra giá trị của một biến với nhiều trường hợp.
- **Cú pháp**:
  ```csharp
  switch (biến)
  {
      case giá_trị_1:
          // Khối mã thực thi
          break;
      case giá_trị_2:
          // Khối mã thực thi
          break;
      default:
          // Khối mã thực thi nếu không khớp giá trị nào
          break;
  }
  ```
- **Ví dụ**: Hiển thị ngày trong tuần.
  ```csharp
  int day = 3;
  switch (day)
  {
      case 1:
          Console.WriteLine("Thứ Hai");
          break;
      case 2:
          Console.WriteLine("Thứ Ba");
          break;
      case 3:
          Console.WriteLine("Thứ Tư");
          break;
      default:
          Console.WriteLine("Ngày không hợp lệ");
          break;
  }
  ```

## 2. Vòng lặp

Vòng lặp cho phép lặp lại một khối mã nhiều lần dựa trên điều kiện.

### 2.1. Vòng lặp `for`

- Dùng khi biết trước số lần lặp.
- **Cú pháp**:
  ```csharp
  for (khởi_tạo; điều_kiện; cập_nhật)
  {
      // Khối mã thực thi
  }
  ```
- **Ví dụ**: In các số từ 1 đến 5.
  ```csharp
  for (int i = 1; i <= 5; i++)
  {
      Console.WriteLine(i);
  }
  ```

### 2.2. Vòng lặp `while`

- Lặp khi điều kiện đúng, kiểm tra điều kiện trước khi thực thi.
- **Cú pháp**:
  ```csharp
  while (điều_kiện)
  {
      // Khối mã thực thi
  }
  ```
- **Ví dụ**: In các số từ 1 đến 5.
  ```csharp
  int i = 1;
  while (i <= 5)
  {
      Console.WriteLine(i);
      i++;
  }
  ```

### 2.3. Vòng lặp `do-while`

- Thực thi khối mã ít nhất một lần, sau đó kiểm tra điều kiện.
- **Cú pháp**:
  ```csharp
  do
  {
      // Khối mã thực thi
  } while (điều_kiện);
  ```
- **Ví dụ**: Nhập số cho đến khi nhập số dương.
  ```csharp
  int number;
  do
  {
      Console.Write("Nhập một số dương: ");
      number = int.Parse(Console.ReadLine());
  } while (number <= 0);
  Console.WriteLine("Bạn đã nhập số dương: {0}", number);
  ```

### 2.4. Vòng lặp `foreach`

- Dùng để duyệt qua các phần tử trong tập hợp (mảng, danh sách...).
- **Cú pháp**:
  ```csharp
  foreach (kiểu_dữ_liệu biến in tập_hợp)
  {
      // Khối mã thực thi
  }
  ```
- **Ví dụ**: Duyệt mảng các số.
  ```csharp
  int[] numbers = { 1, 2, 3, 4, 5 };
  foreach (int num in numbers)
  {
      Console.WriteLine(num);
  }
  ```

## 3. Câu lệnh nhảy

Dùng để thay đổi luồng thực thi của chương trình.

### 3.1. `break`

- Thoát khỏi vòng lặp hoặc `switch-case`.
- **Ví dụ**: Thoát vòng lặp khi gặp số 3.
  ```csharp
  for (int i = 1; i <= 5; i++)
  {
      if (i == 3)
          break;
      Console.WriteLine(i);
  }
  // Kết quả: 1, 2
  ```

### 3.2. `continue`

- Bỏ qua phần còn lại của vòng lặp hiện tại và chuyển sang lần lặp tiếp theo.
- **Ví dụ**: Bỏ qua số 3.
  ```csharp
  for (int i = 1; i <= 5; i++)
  {
      if (i == 3)
          continue;
      Console.WriteLine(i);
  }
  // Kết quả: 1, 2, 4, 5
  ```

### 3.3. `return`

- Thoát khỏi phương thức và trả về giá trị (nếu có).
- **Ví dụ**: Trả về tổng hai số.
  ```csharp
  int Sum(int a, int b)
  {
      return a + b;
  }
  ```

## 4. Bài tập ví dụ

Dưới đây là các bài tập được trình bày trong video để thực hành.

### Bài tập 1: Kiểm tra số nguyên tố

- **Yêu cầu**: Viết chương trình kiểm tra một số nhập vào có phải số nguyên tố hay không.
- **Code**:

  ```csharp
  Console.Write("Nhập một số nguyên dương: ");
  int number = int.Parse(Console.ReadLine());
  bool isPrime = true;

  if (number < 2)
  {
      isPrime = false;
  }
  else
  {
      for (int i = 2; i <= Math.Sqrt(number); i++)
      {
          if (number % i == 0)
          {
              isPrime = false;
              break;
          }
      }
  }

  if (isPrime)
      Console.WriteLine("{0} là số nguyên tố", number);
  else
      Console.WriteLine("{0} không phải là số nguyên tố", number);
  ```

### Bài tập 2: In bảng cửu chương

- **Yêu cầu**: In bảng cửu chương của số nhập vào (từ 1 đến 10).
- **Code**:

  ```csharp
  Console.Write("Nhập số cần in bảng cửu chương: ");
  int number = int.Parse(Console.ReadLine());

  for (int i = 1; i <= 10; i++)
  {
      Console.WriteLine("{0} x {1} = {2}", number, i, number * i);
  }
  ```

### Bài tập 3: Tính tổng các số chẵn từ 1 đến n

- **Yêu cầu**: Nhập số n, tính tổng các số chẵn từ 1 đến n.
- **Code**:

  ```csharp
  Console.Write("Nhập số n: ");
  int n = int.Parse(Console.ReadLine());
  int sum = 0;

  for (int i = 1; i <= n; i++)
  {
      if (i % 2 == 0)
      {
          sum += i;
      }
  }
  Console.WriteLine("Tổng các số chẵn từ 1 đến {0} là: {1}", n, sum);
  ```

## 5. Lưu ý khi sử dụng cấu trúc điều khiển

- **Kiểm tra điều kiện rõ ràng**: Đảm bảo điều kiện trong `if`, `while`, `for` được định nghĩa chính xác để tránh lỗi logic.
- **Tránh lặp vô hạn**: Luôn cập nhật biến điều khiển trong vòng lặp `while` hoặc `for`.
- **Sử dụng `break` và `continue` hợp lý**: Đảm bảo chúng không làm chương trình khó đọc hoặc gây lỗi.
- **Tối ưu hóa hiệu suất**: Với các bài toán lớn, sử dụng thuật toán hiệu quả (ví dụ: kiểm tra số nguyên tố chỉ cần duyệt đến căn bậc hai).

## 6. Tài liệu tham khảo

- Video: [Học lập trình .NET - Phần 4 - Sử dụng các cấu trúc điều khiển](https://www.youtube.com/watch?v=IhijUc2nA6s&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=4)
- Tài liệu chính thức của Microsoft về C#.
