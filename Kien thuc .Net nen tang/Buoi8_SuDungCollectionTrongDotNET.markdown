# Buổi 8: Sử dụng Collection trong .NET

## Mục tiêu

- Hiểu khái niệm Collection trong .NET.
- Nắm được các loại Collection phổ biến và cách sử dụng chúng.
- Thực hành các bài tập ví dụ về Collection.

## Nội dung chính

### 1. Giới thiệu về Collection

- **Collection** là các lớp dùng để lưu trữ, quản lý và thao tác với tập hợp dữ liệu (danh sách, mảng, từ điển, v.v.).
- Trong .NET, Collection nằm trong namespace `System.Collections` và `System.Collections.Generic`.
- Ưu điểm của Collection:
  - Linh hoạt hơn mảng (array) vì có thể thay đổi kích thước động.
  - Cung cấp nhiều phương thức tiện ích để thao tác với dữ liệu.
  - Hỗ trợ các kiểu dữ liệu generic (an toàn kiểu).

### 2. Các loại Collection phổ biến

#### 2.1. System.Collections

- **ArrayList**: Lưu trữ danh sách các đối tượng, không giới hạn kiểu dữ liệu.
- **Hashtable**: Lưu trữ cặp key-value, không an toàn kiểu.
- **Queue**: Hàng đợi (FIFO - First In, First Out).
- **Stack**: Ngăn xếp (LIFO - Last In, First Out).

#### 2.2. System.Collections.Generic

- **List<T>**: Danh sách generic, thay thế ArrayList, an toàn kiểu.
- **Dictionary<TKey, TValue>**: Từ điển generic, thay thế Hashtable.
- **Queue<T>**: Hàng đợi generic.
- **Stack<T>**: Ngăn xếp generic.
- **HashSet<T>**: Tập hợp không trùng lặp, tối ưu cho việc kiểm tra phần tử tồn tại.

### 3. Các thao tác cơ bản với Collection

- **Thêm phần tử**: Add, AddRange, Enqueue, Push.
- **Xóa phần tử**: Remove, RemoveAt, Dequeue, Pop.
- **Truy xuất phần tử**: Indexer, Contains, TryGetValue.
- **Duyệt Collection**: Vòng lặp `foreach`, `for`, hoặc LINQ.

### 4. Ví dụ minh họa

#### 4.1. Sử dụng List<T>

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        // Khởi tạo List
        List<string> danhSachTen = new List<string>();

        // Thêm phần tử
        danhSachTen.Add("Nguyen Van A");
        danhSachTen.Add("Tran Thi B");
        danhSachTen.AddRange(new string[] { "Le Van C", "Pham Thi D" });

        // Duyệt danh sách
        Console.WriteLine("Danh sach ten:");
        foreach (string ten in danhSachTen)
        {
            Console.WriteLine(ten);
        }

        // Xóa phần tử
        danhSachTen.Remove("Tran Thi B");
        Console.WriteLine("\nDanh sach sau khi xoa:");
        foreach (string ten in danhSachTen)
        {
            Console.WriteLine(ten);
        }

        // Kiểm tra tồn tại
        if (danhSachTen.Contains("Nguyen Van A"))
        {
            Console.WriteLine("\nNguyen Van A ton tai trong danh sach.");
        }
    }
}
```

**Kết quả**:

```
Danh sach ten:
Nguyen Van A
Tran Thi B
Le Van C
Pham Thi D

Danh sach sau khi xoa:
Nguyen Van A
Le Van C
Pham Thi D

Nguyen Van A ton tai trong danh sach.
```

#### 4.2. Sử dụng Dictionary<TKey, TValue>

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        // Khởi tạo Dictionary
        Dictionary<int, string> danhSachSV = new Dictionary<int, string>();

        // Thêm phần tử
        danhSachSV.Add(1, "Nguyen Van A");
        danhSachSV.Add(2, "Tran Thi B");
        danhSachSV[3] = "Le Van C"; // Thêm bằng indexer

        // Duyệt Dictionary
        Console.WriteLine("Danh sach sinh vien:");
        foreach (KeyValuePair<int, string> sv in danhSachSV)
        {
            Console.WriteLine($"Ma SV: {sv.Key}, Ten: {sv.Value}");
        }

        // Truy xuất phần tử
        if (danhSachSV.TryGetValue(1, out string ten))
        {
            Console.WriteLine($"\nSinh vien voi Ma SV 1: {ten}");
        }

        // Xóa phần tử
        danhSachSV.Remove(2);
        Console.WriteLine("\nDanh sach sau khi xoa Ma SV 2:");
        foreach (KeyValuePair<int, string> sv in danhSachSV)
        {
            Console.WriteLine($"Ma SV: {sv.Key}, Ten: {sv.Value}");
        }
    }
}
```

**Kết quả**:

```
Danh sach sinh vien:
Ma SV: 1, Ten: Nguyen Van A
Ma SV: 2, Ten: Tran Thi B
Ma SV: 3, Ten: Le Van C

Sinh vien voi Ma SV 1: Nguyen Van A

Danh sach sau khi xoa Ma SV 2:
Ma SV: 1, Ten: Nguyen Van A
Ma SV: 3, Ten: Le Van C
```

#### 4.3. Sử dụng Queue<T>

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        // Khởi tạo Queue
        Queue<string> hangDoi = new Queue<string>();

        // Thêm phần tử
        hangDoi.Enqueue("Khach hang 1");
        hangDoi.Enqueue("Khach hang 2");
        hangDoi.Enqueue("Khach hang 3");

        // Duyệt Queue
        Console.WriteLine("Hang doi khach hang:");
        foreach (string khach in hangDoi)
        {
            Console.WriteLine(khach);
        }

        // Lấy và xóa phần tử đầu tiên
        string khachDau = hangDoi.Dequeue();
        Console.WriteLine($"\nPhuc vu: {khachDau}");

        Console.WriteLine("\nHang doi sau khi phuc vu:");
        foreach (string khach in hangDoi)
        {
            Console.WriteLine(khach);
        }
    }
}
```

**Kết quả**:

```
Hang doi khach hang:
Khach hang 1
Khach hang 2
Khach hang 3

Phuc vu: Khach hang 1

Hang doi sau khi phuc vu:
Khach hang 2
Khach hang 3
```

#### 4.4. Sử dụng Stack<T>

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main(string[] args)
    {
        // Khởi tạo Stack
        Stack<string> nganXep = new Stack<string>();

        // Thêm phần tử
        nganXep.Push("Sach 1");
        nganXep.Push("Sach 2");
        nganXep.Push("Sach 3");

        // Duyệt Stack
        Console.WriteLine("Ngan xep sach:");
        foreach (string sach in nganXep)
        {
            Console.WriteLine(sach);
        }

        // Lấy và xóa phần tử trên cùng
        string sachCuoi = nganXep.Pop();
        Console.WriteLine($"\nLay sach: {sachCuoi}");

        Console.WriteLine("\nNgan xep sau khi lay sach:");
        foreach (string sach in nganXep)
        {
            Console.WriteLine(sach);
        }
    }
}
```

**Kết quả**:

```
Ngan xep sach:
Sach 3
Sach 2
Sach 1

Lay sach: Sach 3

Ngan xep sau khi lay sach:
Sach 2
Sach 1
```

### 5. Bài tập thực hành

1. **Bài tập 1**: Viết chương trình quản lý danh sách sản phẩm sử dụng `List<Product>`, trong đó `Product` có các thuộc tính: `Id`, `Name`, `Price`. Thực hiện:

   - Thêm 5 sản phẩm.
   - Hiển thị danh sách sản phẩm.
   - Xóa sản phẩm có giá nhỏ hơn 100.
   - Tìm sản phẩm theo tên.

2. **Bài tập 2**: Sử dụng `Dictionary<string, int>` để lưu trữ danh sách từ vựng (từ tiếng Anh và số lần xuất hiện). Thực hiện:

   - Thêm 5 từ vựng.
   - Hiển thị danh sách từ vựng.
   - Tìm và hiển thị số lần xuất hiện của một từ cụ thể.
   - Xóa một từ khỏi danh sách.

3. **Bài tập 3**: Sử dụng `Queue<T>` để mô phỏng hàng đợi khách hàng tại quầy thanh toán. Thực hiện:

   - Thêm 5 khách hàng vào hàng đợi.
   - Phục vụ lần lượt từng khách hàng và hiển thị trạng thái hàng đợi.

4. **Bài tập 4**: Sử dụng `Stack<T>` để mô phỏng việc quản lý lịch sử duyệt web. Thực hiện:
   - Thêm 5 URL vào stack.
   - Hiển thị lịch sử duyệt web.
   - Quay lại trang trước đó (pop) và hiển thị trang hiện tại.

### 6. Lưu ý khi sử dụng Collection

- Sử dụng `System.Collections.Generic` thay cho `System.Collections` để đảm bảo an toàn kiểu.
- Chọn loại Collection phù hợp với nhu cầu:
  - `List<T>`: Dùng cho danh sách tuần tự, cần truy xuất theo chỉ số.
  - `Dictionary<TKey, TValue>`: Dùng khi cần tra cứu nhanh theo key.
  - `Queue<T>`: Dùng cho mô phỏng hàng đợi (FIFO).
  - `Stack<T>`: Dùng cho mô phỏng ngăn xếp (LIFO).
- Xử lý ngoại lệ khi truy xuất phần tử không tồn tại (VD: `TryGetValue` thay vì truy xuất trực tiếp trong Dictionary).

### 7. Tài liệu tham khảo

- Tài liệu chính thức của Microsoft về Collections: https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic
- Video hướng dẫn: https://www.youtube.com/watch?v=5bYGm7XZDPw
