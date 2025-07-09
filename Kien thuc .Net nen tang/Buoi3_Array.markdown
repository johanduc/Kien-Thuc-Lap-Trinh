# Học lập trình .NET - Phần 3 - Array

Dưới đây là ghi chép và tổng hợp kiến thức từ video "Học lập trình .NET - Phần 3 - Array" thuộc series hướng dẫn lập trình .NET trên YouTube. Nội dung bao gồm các khái niệm cơ bản về mảng (Array) trong C#, cách sử dụng và các bài tập ví dụ minh họa.

## 1. Giới thiệu về mảng (Array)

### 1.1. Mảng là gì?

- **Mảng (Array)** là một tập hợp các phần tử có cùng kiểu dữ liệu, được lưu trữ liên tiếp trong bộ nhớ.
- Mỗi phần tử trong mảng được truy cập thông qua **chỉ số (index)**, bắt đầu từ **0**.
- Mảng trong C# có kích thước cố định sau khi được khởi tạo.

### 1.2. Đặc điểm của mảng

- **Kích thước cố định**: Không thể thay đổi số lượng phần tử sau khi khai báo.
- **Truy cập nhanh**: Do các phần tử được lưu trữ liên tiếp trong bộ nhớ.
- **Kiểu dữ liệu đồng nhất**: Tất cả phần tử trong mảng phải cùng kiểu (int, string, double, v.v.).

### 1.3. Cú pháp khai báo mảng

```csharp
// Cách 1: Khai báo và khởi tạo mảng
<kiểu_dữ_liệu>[] tên_mảng = new <kiểu_dữ_liệu>[kích_thước];

// Cách 2: Khai báo và gán giá trị trực tiếp
<kiểu_dữ_liệu>[] tên_mảng = { giá_trị_1, giá_trị_2, ..., giá_trị_n };

// Cách 3: Khai báo mảng với kích thước và gán giá trị sau
<kiểu_dữ_liệu>[] tên_mảng = new <kiểu_dữ_liệu>[kích_thước];
tên_mảng[0] = giá_trị_1;
tên_mảng[1] = giá_trị_2;
...
```

**Ví dụ:**

```csharp
int[] numbers = new int[5]; // Khai báo mảng số nguyên có 5 phần tử
numbers[0] = 10;
numbers[1] = 20;
numbers[2] = 30;
numbers[3] = 40;
numbers[4] = 50;

int[] numbers2 = { 10, 20, 30, 40, 50 }; // Khai báo và khởi tạo trực tiếp
```

## 2. Các thao tác cơ bản với mảng

### 2.1. Truy cập phần tử

- Sử dụng chỉ số để truy cập hoặc thay đổi giá trị phần tử:

```csharp
int value = numbers[2]; // Lấy giá trị tại chỉ số 2 (giá trị 30)
numbers[2] = 100; // Gán giá trị mới cho phần tử tại chỉ số 2
```

### 2.2. Duyệt mảng

- **Sử dụng vòng lặp for**:

```csharp
for (int i = 0; i < numbers.Length; i++)
{
    Console.WriteLine(numbers[i]);
}
```

- **Sử dụng vòng lặp foreach** (dành cho duyệt từng phần tử mà không cần chỉ số):

```csharp
foreach (int num in numbers)
{
    Console.WriteLine(num);
}
```

### 2.3. Thuộc tính và phương thức của mảng

- **Length**: Thuộc tính trả về số lượng phần tử trong mảng.

```csharp
int size = numbers.Length; // Trả về 5
```

- **Một số phương thức**:
  - `Array.Sort(tên_mảng)`: Sắp xếp mảng theo thứ tự tăng dần.
  - `Array.Reverse(tên_mảng)`: Đảo ngược thứ tự các phần tử.
  - `Array.IndexOf(tên_mảng, giá_trị)`: Tìm chỉ số đầu tiên của giá trị trong mảng.
  - `Array.Clear(tên_mảng, chỉ_số_bắt_đầu, số_phần_tử)`: Xóa (đặt về giá trị mặc định) các phần tử trong khoảng chỉ định.

**Ví dụ:**

```csharp
int[] arr = { 5, 2, 8, 1, 9 };
Array.Sort(arr); // arr = {1, 2, 5, 8, 9}
Array.Reverse(arr); // arr = {9, 8, 5, 2, 1}
int index = Array.IndexOf(arr, 5); // index = 2
```

## 3. Mảng nhiều chiều

- **Mảng 2 chiều** (ma trận): Là mảng của các mảng.
- **Cú pháp khai báo**:

```csharp
int[,] matrix = new int[số_hàng, số_cột];
```

- **Ví dụ**:

```csharp
int[,] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

// Truy cập phần tử
int value = matrix[1, 2]; // Lấy giá trị 6 (hàng 1, cột 2)
```

- **Duyệt mảng 2 chiều**:

```csharp
for (int i = 0; i < matrix.GetLength(0); i++) // Số hàng
{
    for (int j = 0; j < matrix.GetLength(1); j++) // Số cột
    {
        Console.Write(matrix[i, j] + " ");
    }
    Console.WriteLine();
}
```

- **Mảng răng cưa (Jagged Array)**:
  - Là mảng của các mảng, trong đó mỗi mảng con có thể có kích thước khác nhau.
  - **Cú pháp**:

```csharp
int[][] jaggedArray = new int[3][];
jaggedArray[0] = new int[] { 1, 2, 3 };
jaggedArray[1] = new int[] { 4, 5 };
jaggedArray[2] = new int[] { 6, 7, 8, 9 };
```

## 4. Bài tập ví dụ

### Bài tập 1: Nhập và xuất mảng

**Yêu cầu**: Viết chương trình nhập số lượng phần tử và giá trị các phần tử của mảng số nguyên từ bàn phím, sau đó in ra mảng.

```csharp
Console.Write("Nhap so luong phan tu: ");
int n = int.Parse(Console.ReadLine());
int[] arr = new int[n];

for (int i = 0; i < n; i++)
{
    Console.Write($"Nhap phan tu thu {i}: ");
    arr[i] = int.Parse(Console.ReadLine());
}

Console.WriteLine("Mang vua nhap:");
foreach (int num in arr)
{
    Console.Write(num + " ");
}
```

### Bài tập 2: Tìm phần tử lớn nhất

**Yêu cầu**: Tìm và in ra phần tử lớn nhất trong mảng số nguyên.

```csharp
int[] arr = { 5, 2, 8, 1, 9 };
int max = arr[0];

for (int i = 1; i < arr.Length; i++)
{
    if (arr[i] > max)
    {
        max = arr[i];
    }
}

Console.WriteLine("Phan tu lon nhat: " + max);
```

### Bài tập 3: Sắp xếp mảng

**Yêu cầu**: Sắp xếp mảng theo thứ tự tăng dần và in ra kết quả.

```csharp
int[] arr = { 5, 2, 8, 1, 9 };
Array.Sort(arr);

Console.WriteLine("Mang sau khi sap xep:");
foreach (int num in arr)
{
    Console.Write(num + " ");
}
```

### Bài tập 4: Mảng 2 chiều

**Yêu cầu**: Khai báo mảng 2 chiều 3x3, nhập giá trị từ bàn phím và in ra ma trận.

```csharp
int[,] matrix = new int[3, 3];
Console.WriteLine("Nhap gia tri ma tran 3x3:");
for (int i = 0; i < 3; i++)
{
    for (int j = 0; j < 3; j++)
    {
        Console.Write($"Nhap phan tu [{i},{j}]: ");
        matrix[i, j] = int.Parse(Console.ReadLine());
    }
}

Console.WriteLine("Ma tran vua nhap:");
for (int i = 0; i < 3; i++)
{
    for (int j = 0; j < 3; j++)
    {
        Console.Write(matrix[i, j] + " ");
    }
    Console.WriteLine();
}
```

## 5. Lưu ý khi làm việc với mảng

- **Lỗi IndexOutOfRangeException**: Xảy ra khi truy cập chỉ số ngoài phạm vi mảng. Cần kiểm tra chỉ số hợp lệ trước khi truy cập.
- **Khởi tạo mảng**: Mảng cần được khởi tạo trước khi sử dụng (gán giá trị hoặc khai báo kích thước).
- **Hiệu suất**: Sử dụng mảng phù hợp khi kích thước cố định và không cần thay đổi thường xuyên. Nếu cần thay đổi kích thước, cân nhắc sử dụng `List<T>`.

## 6. Tổng kết

- Mảng là cấu trúc dữ liệu quan trọng trong C#, được sử dụng để lưu trữ và xử lý tập hợp dữ liệu cùng kiểu.
- Các thao tác cơ bản bao gồm truy cập, duyệt, sắp xếp, và tìm kiếm phần tử.
- Mảng nhiều chiều (2D, Jagged Array) hỗ trợ xử lý dữ liệu phức tạp hơn như ma trận hoặc danh sách không đồng đều.
