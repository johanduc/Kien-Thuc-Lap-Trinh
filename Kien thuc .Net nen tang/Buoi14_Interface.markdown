# Buổi 14: Interface trong .NET

## 1. Giới thiệu về Interface

- **Interface** là một hợp đồng (contract) trong lập trình hướng đối tượng, quy định các phương thức mà một lớp phải triển khai.
- Interface không chứa triển khai cụ thể (implementation), chỉ định nghĩa **tên phương thức**, **kiểu trả về**, và **tham số** (nếu có).
- Interface giúp:

  - Đảm bảo tính **đồng nhất** trong việc triển khai các lớp.
  - Tăng tính **linh hoạt** và **bảo trì** của mã nguồn.
  - Hỗ trợ **đa hình** (polymorphism) trong lập trình.

- Trong .NET, interface được khai báo bằng từ khóa `interface` và thường bắt đầu bằng chữ cái **I** (theo quy ước đặt tên, ví dụ: `IComparable`, `IEnumerable`).

### Cú pháp cơ bản

```csharp
interface ITenInterface
{
    void PhuongThuc1();
    int PhuongThuc2(string thamSo);
}
```

- Một lớp có thể **triển khai** (implement) một hoặc nhiều interface bằng cách sử dụng dấu hai chấm `:`.
- Lớp triển khai phải cung cấp định nghĩa cho tất cả các phương thức trong interface.

## 2. Ví dụ cơ bản về Interface

### Ví dụ 1: Interface cho các phương tiện giao thông

- Tạo một interface `IPhuongTien` với phương thức `DiChuyen` và thuộc tính `TocDo`.

```csharp
interface IPhuongTien
{
    void DiChuyen();
    int TocDo { get; set; }
}
```

- Triển khai interface trong các lớp cụ thể như `XeHoi` và `XeDap`.

```csharp
class XeHoi : IPhuongTien
{
    public int TocDo { get; set; }

    public void DiChuyen()
    {
        Console.WriteLine($"Xe hơi di chuyển với tốc độ {TocDo} km/h");
    }
}

class XeDap : IPhuongTien
{
    public int TocDo { get; set; }

    public void DiChuyen()
    {
        Console.WriteLine($"Xe đạp di chuyển với tốc độ {TocDo} km/h");
    }
}
```

- Sử dụng interface trong chương trình chính:

```csharp
class Program
{
    static void Main(string[] args)
    {
        IPhuongTien xeHoi = new XeHoi { TocDo = 100 };
        IPhuongTien xeDap = new XeDap { TocDo = 20 };

        xeHoi.DiChuyen(); // Output: Xe hơi di chuyển với tốc độ 100 km/h
        xeDap.DiChuyen(); // Output: Xe đạp di chuyển với tốc độ 20 km/h
    }
}
```

### Ý nghĩa

- Interface `IPhuongTien` đảm bảo rằng mọi lớp triển khai (`XeHoi`, `XeDap`) đều phải có phương thức `DiChuyen` và thuộc tính `TocDo`.
- Cho phép xử lý các đối tượng khác nhau thông qua một giao diện chung (polymorphism).

## 3. Interface với nhiều lớp triển khai

- Một interface có thể được triển khai bởi nhiều lớp khác nhau, mỗi lớp có cách triển khai riêng.
- Ví dụ: Interface `IHinhHoc` cho các hình học.

```csharp
interface IHinhHoc
{
    double TinhDienTich();
    double TinhChuVi();
}
```

- Triển khai cho hình tròn và hình chữ nhật:

```csharp
class HinhTron : IHinhHoc
{
    public double BanKinh { get; set; }

    public HinhTron(double banKinh)
    {
        BanKinh = banKinh;
    }

    public double TinhDienTich()
    {
        return Math.PI * BanKinh * BanKinh;
    }

    public double TinhChuVi()
    {
        return 2 * Math.PI * BanKinh;
    }
}

class HinhChuNhat : IHinhHoc
{
    public double ChieuDai { get; set; }
    public double ChieuRong { get; set; }

    public HinhChuNhat(double chieuDai, double chieuRong)
    {
        ChieuDai = chieuDai;
        ChieuRong = chieuRong;
    }

    public double TinhDienTich()
    {
        return ChieuDai * ChieuRong;
    }

    public double TinhChuVi()
    {
        return 2 * (ChieuDai + ChieuRong);
    }
}
```

- Sử dụng trong chương trình:

```csharp
class Program
{
    static void Main(string[] args)
    {
        IHinhHoc hinhTron = new HinhTron(5);
        IHinhHoc hinhChuNhat = new HinhChuNhat(4, 6);

        Console.WriteLine($"Diện tích hình tròn: {hinhTron.TinhDienTich()}"); // Output: ~78.54
        Console.WriteLine($"Chu vi hình tròn: {hinhTron.TinhChuVi()}"); // Output: ~31.42
        Console.WriteLine($"Diện tích hình chữ nhật: {hinhChuNhat.TinhDienTich()}"); // Output: 24
        Console.WriteLine($"Chu vi hình chữ nhật: {hinhChuNhat.TinhChuVi()}"); // Output: 20
    }
}
```

## 4. Interface và Đa hình

- Interface cho phép sử dụng đa hình, tức là xử lý các đối tượng khác nhau thông qua một kiểu interface chung.
- Ví dụ: Sử dụng danh sách các đối tượng `IHinhHoc` để tính diện tích và chu vi.

```csharp
class Program
{
    static void Main(string[] args)
    {
        List<IHinhHoc> danhSachHinh = new List<IHinhHoc>
        {
            new HinhTron(5),
            new HinhChuNhat(4, 6)
        };

        foreach (var hinh in danhSachHinh)
        {
            Console.WriteLine($"Diện tích: {hinh.TinhDienTich()}, Chu vi: {hinh.TinhChuVi()}");
        }
    }
}
```

- **Kết quả**:
  - Diện tích hình tròn: ~78.54, Chu vi: ~31.42
  - Diện tích hình chữ nhật: 24, Chu vi: 20

## 5. Kế thừa nhiều Interface

- Một lớp có thể triển khai nhiều interface, nhưng chỉ có thể kế thừa từ **một lớp cơ sở**.
- Ví dụ: Interface cho khả năng hiển thị thông tin và di chuyển.

```csharp
interface IPhuongTien
{
    void DiChuyen();
}

interface IHienThi
{
    void HienThiThongTin();
}

class XeHoi : IPhuongTien, IHienThi
{
    public string Ten { get; set; }
    public int TocDo { get; set; }

    public XeHoi(string ten, int tocDo)
    {
        Ten = ten;
        TocDo = tocDo;
    }

    public void DiChuyen()
    {
        Console.WriteLine($"{Ten} di chuyển với tốc độ {TocDo} km/h");
    }

    public void HienThiThongTin()
    {
        Console.WriteLine($"Phương tiện: {Ten}, Tốc độ: {TocDo} km/h");
    }
}
```

- Sử dụng:

```csharp
class Program
{
    static void Main(string[] args)
    {
        XeHoi xe = new XeHoi("Toyota", 120);
        xe.DiChuyen(); // Output: Toyota di chuyển với tốc độ 120 km/h
        xe.HienThiThongTin(); // Output: Phương tiện: Toyota, Tốc độ: 120 km/h
    }
}
```

## 6. Explicit Interface Implementation

- Khi một lớp triển khai nhiều interface có phương thức trùng tên, ta có thể sử dụng **explicit interface implementation** để phân biệt.

```csharp
interface IInterface1
{
    void HienThi();
}

interface IInterface2
{
    void HienThi();
}

class MyClass : IInterface1, IInterface2
{
    void IInterface1.HienThi()
    {
        Console.WriteLine("Triển khai cho IInterface1");
    }

    void IInterface2.HienThi()
    {
        Console.WriteLine("Triển khai cho IInterface2");
    }
}
```

- Sử dụng:

```csharp
class Program
{
    static void Main(string[] args)
    {
        IInterface1 obj1 = new MyClass();
        IInterface2 obj2 = new MyClass();

        obj1.HienThi(); // Output: Triển khai cho IInterface1
        obj2.HienThi(); // Output: Triển khai cho IInterface2
    }
}
```

## 7. Interface trong thực tế

- Interface được sử dụng rộng rãi trong .NET, ví dụ:
  - `IComparable`: So sánh các đối tượng.
  - `IEnumerable`: Duyệt qua tập hợp.
  - `IDisposable`: Quản lý tài nguyên.
- Interface giúp tách biệt **giao diện** (interface) và **triển khai** (implementation), tăng tính tái sử dụng và dễ dàng kiểm thử (unit testing).

## 8. Bài tập ví dụ

### Bài tập 1: Interface cho động vật

- Tạo interface `IDongVat` với các phương thức:
  - `Keu()`: In ra âm thanh của động vật.
  - `DiChuyen()`: In ra cách di chuyển của động vật.
- Triển khai cho hai lớp `Cho` và `Meo`.
- Viết chương trình chính để kiểm tra.

**Giải pháp**:

```csharp
interface IDongVat
{
    void Keu();
    void DiChuyen();
}

class Cho : IDongVat
{
    public void Keu()
    {
        Console.WriteLine("Gâu gâu!");
    }

    public void DiChuyen()
    {
        Console.WriteLine("Chó chạy bằng 4 chân.");
    }
}

class Meo : IDongVat
{
    public void Keu()
    {
        Console.WriteLine("Meo meo!");
    }

    public void DiChuyen()
    {
        Console.WriteLine("Mèo đi nhẹ nhàng bằng 4 chân.");
    }
}

class Program
{
    static void Main(string[] args)
    {
        List<IDongVat> dongVats = new List<IDongVat>
        {
            new Cho(),
            new Meo()
        };

        foreach (var dongVat in dongVats)
        {
            dongVat.Keu();
            dongVat.DiChuyen();
        }
    }
}
```

**Kết quả**:

```
Gâu gâu!
Chó chạy bằng 4 chân.
Meo meo!
Mèo đi nhẹ nhàng bằng 4 chân.
```

### Bài tập 2: Interface cho thiết bị điện tử

- Tạo interface `IThietBi` với các phương thức:
  - `Bat()`: Bật thiết bị.
  - `Tat()`: Tắt thiết bị.
- Triển khai cho lớp `Tivi` và `DieuHoa`.
- Viết chương trình chính để kiểm tra.

**Giải pháp**:

```csharp
interface IThietBi
{
    void Bat();
    void Tat();
}

class Tivi : IThietBi
{
    public void Bat()
    {
        Console.WriteLine("Tivi được bật.");
    }

    public void Tat()
    {
        Console.WriteLine("Tivi được tắt.");
    }
}

class DieuHoa : IThietBi
{
    public void Bat()
    {
        Console.WriteLine("Điều hòa được bật.");
    }

    public void Tat()
    {
        Console.WriteLine("Điều hòa được tắt.");
    }
}

class Program
{
    static void Main(string[] args)
    {
        IThietBi tivi = new Tivi();
        IThietBi dieuHoa = new DieuHoa();

        tivi.Bat(); // Output: Tivi được bật.
        tivi.Tat(); // Output: Tivi được tắt.
        dieuHoa.Bat(); // Output: Điều hòa được bật.
        dieuHoa.Tat(); // Output: Điều hòa được tắt.
    }
}
```

## 9. Lưu ý quan trọng

- Interface không thể chứa **trường (field)**, chỉ có thể chứa **thuộc tính (property)**, **phương thức (method)**, hoặc **sự kiện (event)**.
- Interface không thể được khởi tạo trực tiếp (không có constructor).
- Tất cả thành viên của interface mặc định là **public** và không cần khai báo từ khóa `public`.
- Một interface có thể kế thừa từ interface khác.

```csharp
interface IBaseInterface
{
    void Method1();
}

interface IDerivedInterface : IBaseInterface
{
    void Method2();
}
```

- Lớp triển khai `IDerivedInterface` phải triển khai cả `Method1` và `Method2`.

## 10. Kết luận

- Interface là công cụ mạnh mẽ trong .NET để đảm bảo tính thống nhất và linh hoạt trong thiết kế phần mềm.
- Sử dụng interface giúp mã nguồn dễ mở rộng, dễ bảo trì, và hỗ trợ tốt cho việc phát triển theo hướng đa hình.
- Các bài tập thực hành giúp hiểu rõ cách áp dụng interface vào các tình huống thực tế.
