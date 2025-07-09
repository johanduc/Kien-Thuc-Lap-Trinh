# Buổi 22: Sử dụng các chỉ dẫn tiền biên dịch

## 1. Giới thiệu về chỉ dẫn tiền biên dịch
Chỉ dẫn tiền biên dịch (Preprocessor Directives) là các lệnh đặc biệt trong C# được xử lý bởi trình biên dịch trước khi biên dịch mã nguồn thực tế. Các chỉ dẫn này bắt đầu bằng ký hiệu `#` và được sử dụng để:
- Điều khiển quá trình biên dịch.
- Bật/tắt các đoạn mã dựa trên điều kiện.
- Hỗ trợ debug, cấu hình, và tối ưu hóa mã nguồn.

### Đặc điểm chính:
- Không phải là câu lệnh thực thi (không kết thúc bằng dấu `;`).
- Được xử lý trước khi chương trình được biên dịch.
- Thường dùng để quản lý mã nguồn trong các tình huống như debug, release, hoặc các nền tảng khác nhau.

## 2. Các chỉ dẫn tiền biên dịch phổ biến

### 2.1. `#define` và `#undef`
- **#define**: Định nghĩa một biểu tượng (symbol) để sử dụng trong các chỉ dẫn điều kiện.
- **#undef**: Xóa định nghĩa của một biểu tượng.
- Vị trí: Thường đặt ở đầu file mã nguồn, trước bất kỳ mã thực thi nào.

**Ví dụ**:
```csharp
#define DEBUG_MODE

using System;

class Program
{
    static void Main()
    {
        #if DEBUG_MODE
            Console.WriteLine("Đang chạy ở chế độ Debug");
        #else
            Console.WriteLine("Đang chạy ở chế độ Release");
        #endif
    }
}
```
- Trong ví dụ trên, nếu `DEBUG_MODE` được định nghĩa, chương trình sẽ in ra "Đang chạy ở chế độ Debug". Nếu không, sẽ in "Đang chạy ở chế độ Release".
- **Lưu ý**: Có thể xóa định nghĩa bằng `#undef DEBUG_MODE` để tắt biểu tượng.

### 2.2. `#if`, `#else`, `#elif`, `#endif`
- **#if**: Kiểm tra một biểu tượng có được định nghĩa hay không.
- **#else**: Xử lý trường hợp ngược lại của `#if`.
- **#elif**: Kết hợp giữa `#else` và `#if`, kiểm tra điều kiện khác nếu điều kiện `#if` trước đó không thỏa mãn.
- **#endif**: Kết thúc khối điều kiện.

**Ví dụ**:
```csharp
#define TEST

using System;

class Program
{
    static void Main()
    {
        #if TEST
            Console.WriteLine("Chế độ TEST đang được bật");
        #elif DEBUG_MODE
            Console.WriteLine("Chế độ DEBUG_MODE đang được bật");
        #else
            Console.WriteLine("Không có chế độ nào được bật");
        #endif
    }
}
```
- Nếu `TEST` được định nghĩa, chỉ dòng "Chế độ TEST đang được bật" được biên dịch. Nếu không, chương trình sẽ kiểm tra tiếp `DEBUG_MODE`, và nếu không có biểu tượng nào, nó sẽ in dòng trong `#else`.

### 2.3. `#region` và `#endregion`
- **#region**: Đánh dấu một vùng mã có thể thu gọn hoặc mở rộng trong các IDE (như Visual Studio).
- **#endregion**: Kết thúc vùng mã.
- Mục đích: Giúp tổ chức mã nguồn, dễ đọc hơn, đặc biệt trong các file lớn.

**Ví dụ**:
```csharp
using System;

class Program
{
    #region Main Program
    static void Main()
    {
        Console.WriteLine("Chào mừng bạn đến với chương trình!");
        #region Nested Region
        Console.WriteLine("Đây là một vùng lồng nhau");
        #endregion
    }
    #endregion
}
```
- Trong Visual Studio, các vùng được đánh dấu bởi `#region` có thể thu gọn để cải thiện khả năng đọc mã.

### 2.4. `#warning` và `#error`
- **#warning**: Tạo một cảnh báo (warning) trong quá trình biên dịch.
- **#error**: Tạo một lỗi (error) và dừng quá trình biên dịch.
- Mục đích: Thông báo cho lập trình viên về các vấn đề tiềm ẩn hoặc yêu cầu kiểm tra mã.

**Ví dụ**:
```csharp
#define OLD_VERSION

using System;

class Program
{
    static void Main()
    {
        #if OLD_VERSION
            #warning Phiên bản này đã cũ, vui lòng cập nhật
        #else
            Console.WriteLine("Phiên bản mới nhất");
        #endif
    }
}
```
- Nếu `OLD_VERSION` được định nghĩa, trình biên dịch sẽ hiển thị cảnh báo: "Phiên bản này đã cũ, vui lòng cập nhật".

**Ví dụ với `#error`**:
```csharp
#define INCOMPLETE_CODE

using System;

class Program
{
    static void Main()
    {
        #if INCOMPLETE_CODE
            #error Mã chưa hoàn thiện, không thể biên dịch
        #else
            Console.WriteLine("Mã đã hoàn thiện");
        #endif
    }
}
```
- Nếu `INCOMPLETE_CODE` được định nghĩa, trình biên dịch sẽ dừng và báo lỗi: "Mã chưa hoàn thiện, không thể biên dịch".

### 2.5. `#line`
- **#line**: Thay đổi số dòng và tên file được báo cáo trong thông báo lỗi hoặc cảnh báo của trình biên dịch.
- Mục đích: Hữu ích khi mã được tạo tự động (ví dụ: từ các công cụ sinh mã).

**Ví dụ**:
```csharp
using System;

class Program
{
    static void Main()
    {
        #line 100 "CustomFile.cs"
        Console.WriteLine("Dòng này sẽ được báo cáo là dòng 100 trong CustomFile.cs");
        #line default
        Console.WriteLine("Quay lại số dòng mặc định");
    }
}
```
- Nếu có lỗi xảy ra trong dòng `Console.WriteLine`, trình biên dịch sẽ báo lỗi ở dòng 100 của file "CustomFile.cs".

### 2.6. `#pragma`
- **#pragma**: Điều khiển các hành vi cụ thể của trình biên dịch, ví dụ như bỏ qua cảnh báo.
- **#pragma warning**: Bật/tắt các cảnh báo cụ thể.

**Ví dụ**:
```csharp
using System;

class Program
{
    #pragma warning disable CS0219
    static void Main()
    {
        int unusedVariable = 10; // Biến không được sử dụng, nhưng cảnh báo bị tắt
        Console.WriteLine("Chương trình chạy bình thường");
    }
    #pragma warning restore CS0219
}
```
- Trong ví dụ trên, cảnh báo CS0219 (biến không được sử dụng) bị tắt trong khối mã, nên trình biên dịch không báo lỗi.

## 3. Ứng dụng thực tế
- **Debugging**: Sử dụng `#if DEBUG` để chỉ biên dịch mã debug khi cần.
- **Cấu hình đa nền tảng**: Dùng các biểu tượng như `NETCORE`, `NETFRAMEWORK` để viết mã phù hợp với các nền tảng khác nhau.
- **Tổ chức mã nguồn**: Sử dụng `#region` để làm mã dễ đọc hơn.
- **Kiểm soát lỗi/cảnh báo**: Dùng `#warning` và `#error` để đảm bảo mã được kiểm tra trước khi phát hành.

## 4. Bài tập ví dụ
### Bài tập 1: Sử dụng `#define` và `#if`
Viết chương trình kiểm tra chế độ chạy (DEBUG hoặc RELEASE) và in thông báo tương ứng.

```csharp
#define DEBUG

using System;

class Program
{
    static void Main()
    {
        #if DEBUG
            Console.WriteLine("Chạy ở chế độ DEBUG");
        #else
            Console.WriteLine("Chạy ở chế độ RELEASE");
        #endif
    }
}
```

### Bài tập 2: Sử dụng `#region`
Tạo một chương trình với hai vùng mã: một cho nhập liệu và một cho xử lý logic.

```csharp
using System;

class Program
{
    #region Input Section
    static string GetUserInput()
    {
        Console.WriteLine("Nhập tên của bạn:");
        return Console.ReadLine();
    }
    #endregion

    #region Logic Section
    static void ProcessInput(string name)
    {
        Console.WriteLine($"Xin chào, {name}!");
    }
    #endregion

    static void Main()
    {
        string name = GetUserInput();
        ProcessInput(name);
    }
}
```

### Bài tập 3: Sử dụng `#warning` và `#error`
Viết chương trình kiểm tra xem mã có đang sử dụng phiên bản cũ hay không.

```csharp
#define OLD_VERSION

using System;

class Program
{
    static void Main()
    {
        #if OLD_VERSION
            #warning Cảnh báo: Bạn đang sử dụng phiên bản cũ
            Console.WriteLine("Vui lòng cập nhật phiên bản mới");
        #else
            Console.WriteLine("Bạn đang sử dụng phiên bản mới nhất");
        #endif
    }
}
```

## 5. Lưu ý khi sử dụng chỉ dẫn tiền biên dịch
- **Không lạm dụng**: Chỉ nên dùng khi cần thiết, vì lạm dụng có thể làm mã khó đọc.
- **Tính tương thích**: Một số chỉ dẫn như `#define` có thể không hoạt động trong một số trình biên dịch hoặc môi trường.
- **Tổ chức mã**: Sử dụng `#region` một cách hợp lý để tránh làm mã trở nên rối rắm.
- **Kiểm tra biểu tượng**: Đảm bảo các biểu tượng (`DEBUG`, `TEST`,...) được định nghĩa hoặc xóa đúng cách.