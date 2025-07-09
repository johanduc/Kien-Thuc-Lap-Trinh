# Buổi 7: Stream và File

## 1. Tổng quan về Stream

- **Stream** là một khái niệm quan trọng trong lập trình, đặc biệt khi làm việc với dữ liệu như file, mạng, hoặc bộ nhớ.
- Stream là một chuỗi các byte dữ liệu, được sử dụng để đọc hoặc ghi dữ liệu theo cách tuần tự (sequential).
- **Ví dụ thực tế**: Khi xem video trên YouTube, dữ liệu video được truyền qua mạng dưới dạng stream, không phải tải toàn bộ file về trước.
- Stream giúp tiết kiệm tài nguyên vì không cần tải toàn bộ dữ liệu vào bộ nhớ cùng lúc.

### Các đặc điểm của Stream:

- **Sequential Access**: Dữ liệu được đọc hoặc ghi tuần tự, không thể nhảy trực tiếp đến một vị trí cụ thể mà không đi qua các byte trước đó.
- **Input/Output**: Stream có thể là input (đọc dữ liệu) hoặc output (ghi dữ liệu).
- **Abstraction**: Stream trừu tượng hóa các nguồn dữ liệu như file, mạng, hoặc bộ nhớ.

### Các loại Stream trong .NET:

- **FileStream**: Làm việc với file trên đĩa.
- **MemoryStream**: Làm việc với dữ liệu trong bộ nhớ.
- **NetworkStream**: Làm việc với dữ liệu qua mạng.
- **BufferedStream**: Tăng hiệu suất bằng cách sử dụng bộ đệm.

## 2. Các lớp liên quan đến Stream trong .NET

- **System.IO**: Namespace chính chứa các lớp để làm việc với Stream và File.
- Các lớp chính:
  - `Stream`: Lớp trừu tượng cơ bản, các lớp khác kế thừa từ đây.
  - `FileStream`: Dùng để đọc/ghi file.
  - `MemoryStream`: Dùng để đọc/ghi dữ liệu trong bộ nhớ.
  - `StreamReader`/`StreamWriter`: Dùng để đọc/ghi dữ liệu dạng văn bản (text).
  - `BinaryReader`/`BinaryWriter`: Dùng để đọc/ghi dữ liệu dạng nhị phân.

## 3. Làm việc với FileStream

- `FileStream` cho phép đọc/ghi dữ liệu vào file trên đĩa.
- **Constructor chính**:

  ```csharp
  FileStream fs = new FileStream(string path, FileMode mode, FileAccess access, FileShare share);
  ```

  - `path`: Đường dẫn đến file.
  - `FileMode`: Chế độ mở file (Create, Open, Append, v.v.).
  - `FileAccess`: Quyền truy cập (Read, Write, ReadWrite).
  - `FileShare`: Cho phép các tiến trình khác truy cập file cùng lúc (Read, Write, None).

### Ví dụ 1: Ghi dữ liệu vào file bằng FileStream

```csharp
using System;
using System.IO;
using System.Text;

class Program
{
    static void Main()
    {
        string path = "example.txt";
        string data = "Hello, this is a test file!";

        // Chuyển string thành mảng byte
        byte[] bytes = Encoding.UTF8.GetBytes(data);

        // Tạo FileStream để ghi
        using (FileStream fs = new FileStream(path, FileMode.Create, FileAccess.Write))
        {
            fs.Write(bytes, 0, bytes.Length);
        }

        Console.WriteLine("Data written to file successfully.");
    }
}
```

**Giải thích**:

- `Encoding.UTF8.GetBytes`: Chuyển chuỗi thành mảng byte để ghi vào file.
- `FileMode.Create`: Tạo mới file, nếu file đã tồn tại thì ghi đè.
- `using`: Đảm bảo FileStream được đóng đúng cách sau khi sử dụng.
- Kết quả: File `example.txt` được tạo với nội dung "Hello, this is a test file!".

### Ví dụ 2: Đọc dữ liệu từ file bằng FileStream

```csharp
using System;
using System.IO;
using System.Text;

class Program
{
    static void Main()
    {
        string path = "example.txt";

        // Tạo FileStream để đọc
        using (FileStream fs = new FileStream(path, FileMode.Open, FileAccess.Read))
        {
            byte[] bytes = new byte[fs.Length];
            fs.Read(bytes, 0, bytes.Length);

            // Chuyển mảng byte thành string
            string data = Encoding.UTF8.GetString(bytes);
            Console.WriteLine("Data read from file: " + data);
        }
    }
}
```

**Giải thích**:

- `fs.Length`: Lấy kích thước file để tạo mảng byte phù hợp.
- `fs.Read`: Đọc dữ liệu từ file vào mảng byte.
- `Encoding.UTF8.GetString`: Chuyển mảng byte thành chuỗi.
- Kết quả: In ra nội dung file `example.txt`.

## 4. Làm việc với StreamWriter và StreamReader

- `StreamWriter` và `StreamReader` được thiết kế để làm việc với dữ liệu dạng văn bản, dễ sử dụng hơn FileStream khi xử lý chuỗi.
- Chúng tự động xử lý encoding (mặc định là UTF-8).

### Ví dụ 3: Ghi văn bản vào file bằng StreamWriter

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "textfile.txt";

        // Tạo StreamWriter để ghi
        using (StreamWriter sw = new StreamWriter(path))
        {
            sw.WriteLine("This is line 1.");
            sw.WriteLine("This is line 2.");
            sw.Write("This is line 3 without newline.");
        }

        Console.WriteLine("Text written to file successfully.");
    }
}
```

**Giải thích**:

- `sw.WriteLine`: Ghi một dòng và thêm ký tự xuống dòng.
- `sw.Write`: Ghi văn bản mà không thêm ký tự xuống dòng.
- Kết quả: File `textfile.txt` chứa:

  ```
  This is line 1.
  This is line 2.
  This is line 3 without newline.
  ```

### Ví dụ 4: Đọc văn bản từ file bằng StreamReader

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "textfile.txt";

        // Tạo StreamReader để đọc
        using (StreamReader sr = new StreamReader(path))
        {
            string line;
            while ((line = sr.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }
    }
}
```

**Giải thích**:

- `sr.ReadLine`: Đọc từng dòng trong file cho đến khi gặp null (hết file).
- Kết quả: In ra từng dòng trong file `textfile.txt`.

## 5. Làm việc với BinaryWriter và BinaryReader

- `BinaryWriter` và `BinaryReader` dùng để đọc/ghi dữ liệu nhị phân (số nguyên, số thực, chuỗi, v.v.).
- Phù hợp khi cần lưu trữ dữ liệu không phải dạng văn bản thuần túy.

### Ví dụ 5: Ghi dữ liệu nhị phân bằng BinaryWriter

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "binaryfile.dat";

        // Tạo BinaryWriter để ghi
        using (BinaryWriter bw = new BinaryWriter(File.Open(path, FileMode.Create)))
        {
            bw.Write(123); // Ghi số nguyên
            bw.Write(45.67); // Ghi số thực
            bw.Write("Hello Binary!"); // Ghi chuỗi
        }

        Console.WriteLine("Binary data written to file successfully.");
    }
}
```

**Giải thích**:

- `bw.Write`: Ghi các kiểu dữ liệu khác nhau (int, double, string) vào file nhị phân.
- Kết quả: File `binaryfile.dat` chứa dữ liệu nhị phân.

### Ví dụ 6: Đọc dữ liệu nhị phân bằng BinaryReader

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "binaryfile.dat";

        // Tạo BinaryReader để đọc
        using (BinaryReader br = new BinaryReader(File.Open(path, FileMode.Open)))
        {
            int number = br.ReadInt32(); // Đọc số nguyên
            double value = br.ReadDouble(); // Đọc số thực
            string text = br.ReadString(); // Đọc chuỗi

            Console.WriteLine($"Number: {number}");
            Console.WriteLine($"Value: {value}");
            Console.WriteLine($"Text: {text}");
        }
    }
}
```

**Giải thích**:

- `br.ReadInt32`, `br.ReadDouble`, `br.ReadString`: Đọc các kiểu dữ liệu tương ứng từ file nhị phân.
- Kết quả: In ra:

  ```
  Number: 123
  Value: 45.67
  Text: Hello Binary!
  ```

## 6. Xử lý ngoại lệ khi làm việc với file

- Khi làm việc với file, cần xử lý các ngoại lệ như:
  - `FileNotFoundException`: File không tồn tại.
  - `IOException`: Lỗi đọc/ghi file.
  - `UnauthorizedAccessException`: Không có quyền truy cập.

### Ví dụ 7: Xử lý ngoại lệ

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string path = "nonexistent.txt";

        try
        {
            using (StreamReader sr = new StreamReader(path))
            {
                Console.WriteLine(sr.ReadToEnd());
            }
        }
        catch (FileNotFoundException)
        {
            Console.WriteLine("Error: File not found.");
        }
        catch (IOException ex)
        {
            Console.WriteLine($"IO Error: {ex.Message}");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Unexpected Error: {ex.Message}");
        }
    }
}
```

**Giải thích**:

- `try-catch`: Bắt các ngoại lệ có thể xảy ra khi đọc file.
- Kết quả: Nếu file không tồn tại, in ra "Error: File not found.".

## 7. Một số phương thức và lớp tiện ích

- **Lớp** `File`:
  - `File.WriteAllText(path, content)`: Ghi toàn bộ chuỗi vào file.
  - `File.ReadAllText(path)`: Đọc toàn bộ nội dung file thành chuỗi.
  - `File.WriteAllBytes(path, bytes)`: Ghi mảng byte vào file.
  - `File.ReadAllBytes(path)`: Đọc toàn bộ file thành mảng byte.
- **Lớp** `Path`:
  - `Path.Combine(path1, path2)`: Kết hợp các đường dẫn.
  - `Path.GetFileName(path)`: Lấy tên file từ đường dẫn.
  - `Path.GetDirectoryName(path)`: Lấy thư mục chứa file.

### Ví dụ 8: Sử dụng lớp File và Path

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string folder = "C:\\Temp";
        string fileName = "test.txt";
        string fullPath = Path.Combine(folder, fileName);

        // Ghi file
        File.WriteAllText(fullPath, "This is a test content.");

        // Đọc file
        string content = File.ReadAllText(fullPath);
        Console.WriteLine($"Content: {content}");

        // Lấy thông tin
        Console.WriteLine($"File Name: {Path.GetFileName(fullPath)}");
        Console.WriteLine($"Directory: {Path.GetDirectoryName(fullPath)}");
    }
}
```

**Giải thích**:

- `Path.Combine`: Tạo đường dẫn an toàn, tránh lỗi do dấu phân cách.
- `File.WriteAllText` và `File.ReadAllText`: Thao tác đơn giản với file văn bản.
- Kết quả: Ghi và đọc file, in thông tin về file và thư mục.

## 8. Bài tập thực hành

1. **Bài tập 1**: Viết chương trình đọc nội dung từ file `input.txt`, chuyển tất cả các ký tự thành chữ hoa, và ghi kết quả vào file `output.txt`.
2. **Bài tập 2**: Viết chương trình sử dụng `BinaryWriter` để ghi thông tin của một sinh viên (ID: int, Name: string, GPA: double) vào file `student.dat`. Sau đó, sử dụng `BinaryReader` để đọc và hiển thị thông tin.
3. **Bài tập 3**: Viết chương trình sử dụng `File.WriteAllLines` để ghi danh sách các số từ 1 đến 100 vào file `numbers.txt`, mỗi số trên một dòng.

### Gợi ý giải bài tập 1

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string inputPath = "input.txt";
        string outputPath = "output.txt";

        try
        {
            string content = File.ReadAllText(inputPath);
            string upperContent = content.ToUpper();

            File.WriteAllText(outputPath, upperContent);
            Console.WriteLine("Conversion completed successfully.");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error: {ex.Message}");
        }
    }
}
```

## 9. Tóm tắt kiến thức

- **Stream**: Chuỗi byte dữ liệu, dùng để đọc/ghi tuần tự.
- **FileStream**: Đọc/ghi file ở mức thấp (byte).
- **StreamWriter/StreamReader**: Đọc/ghi file văn bản.
- **BinaryWriter/BinaryReader**: Đọc/ghi dữ liệu nhị phân.
- **Lớp File và Path**: Cung cấp các phương thức tiện ích để thao tác với file.
- **Xử lý ngoại lệ**: Luôn sử dụng try-catch khi làm việc với file để tránh lỗi.

## 10. Lưu ý khi làm việc với Stream

- Luôn sử dụng `using` để đảm bảo tài nguyên (file, stream) được giải phóng.
- Chọn đúng loại Stream phù hợp với nhu cầu (text, binary, memory, v.v.).
- Kiểm tra quyền truy cập và sự tồn tại của file trước khi thao tác.
- Sử dụng các phương thức tiện ích của lớp `File` cho các tác vụ đơn giản.

---

_Ghi chú_: Video này thuộc playlist “Học lập trình .NET” trên YouTube, cung cấp kiến thức cơ bản và thực hành về Stream và FileStream trong .NET Framework. Nội dung phù hợp cho người mới bắt đầu học lập trình C#.
