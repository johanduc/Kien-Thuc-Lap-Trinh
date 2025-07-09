# Buổi 10: LINQ

## Mục tiêu

- Hiểu được LINQ là gì và vai trò của nó trong lập trình .NET.
- Nắm vững cách sử dụng LINQ để truy vấn dữ liệu từ các nguồn khác nhau (List, Array, XML, Database).
- Thực hành các ví dụ về LINQ với cú pháp Query và Method.

## Nội dung chính

### 1. LINQ là gì?

- **LINQ** (Language Integrated Query) là một tính năng của .NET Framework cho phép truy vấn dữ liệu từ nhiều nguồn khác nhau (List, Array, XML, SQL Server, v.v.) bằng cú pháp tích hợp trong C#.
- LINQ giúp viết mã ngắn gọn, dễ đọc và thống nhất khi xử lý dữ liệu.
- Hai cách sử dụng LINQ:
  - **Query Syntax**: Giống cú pháp SQL, dễ đọc, thường được sử dụng cho người mới.
  - **Method Syntax**: Sử dụng các phương thức như `Where`, `Select`, `OrderBy`, v.v., linh hoạt hơn.

### 2. Các thành phần chính của LINQ

- **Data Source**: Nguồn dữ liệu (List, Array, XML, Database, v.v.).
- **Query**: Câu truy vấn để lọc, sắp xếp, hoặc biến đổi dữ liệu.
- **Execution**: Thực thi truy vấn để lấy kết quả (khi truy cập dữ liệu, ví dụ: `ToList()`, `ToArray()`, hoặc vòng lặp `foreach`).

### 3. Các kiểu LINQ

- **LINQ to Objects**: Truy vấn trên các tập hợp trong bộ nhớ (List, Array).
- **LINQ to XML**: Truy vấn và thao tác với dữ liệu XML.
- **LINQ to SQL**: Truy vấn cơ sở dữ liệu SQL Server.
- **LINQ to Entities**: Truy vấn dữ liệu thông qua Entity Framework.

### 4. Cú pháp cơ bản của LINQ

#### Query Syntax

```csharp
var query = from <biến> in <nguồn_dữ_liệu>
            where <điều_kiện>
            orderby <trường_sắp_xếp>
            select <kết_quả>;
```

#### Method Syntax

```csharp
var query = nguồn_dữ_liệu
            .Where(x => điều_kiện)
            .OrderBy(x => trường_sắp_xếp)
            .Select(x => kết_quả);
```

### 5. Ví dụ thực hành

#### Ví dụ 1: Lọc danh sách số chẵn từ mảng

```csharp
int[] numbers = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

// Query Syntax
var evenNumbersQuery = from num in numbers
                       where num % 2 == 0
                       select num;

// Method Syntax
var evenNumbersMethod = numbers.Where(num => num % 2 == 0);

Console.WriteLine("Số chẵn (Query Syntax):");
foreach (var num in evenNumbersQuery)
{
    Console.WriteLine(num);
}

Console.WriteLine("Số chẵn (Method Syntax):");
foreach (var num in evenNumbersMethod)
{
    Console.WriteLine(num);
}
```

**Kết quả**:

```
Số chẵn (Query Syntax):
2
4
6
8
10
Số chẵn (Method Syntax):
2
4
6
8
10
```

#### Ví dụ 2: Truy vấn danh sách sinh viên

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}

List<Student> students = new List<Student>
{
    new Student { Id = 1, Name = "An", Age = 20 },
    new Student { Id = 2, Name = "Bình", Age = 22 },
    new Student { Id = 3, Name = "Cường", Age = 19 },
    new Student { Id = 4, Name = "Dung", Age = 21 }
};

// Query Syntax: Lọc sinh viên có tuổi > 20 và sắp xếp theo tên
var queryStudents = from student in students
                    where student.Age > 20
                    orderby student.Name
                    select student;

// Method Syntax
var methodStudents = students
                    .Where(s => s.Age > 20)
                    .OrderBy(s => s.Name);

Console.WriteLine("Sinh viên trên 20 tuổi (Query Syntax):");
foreach (var student in queryStudents)
{
    Console.WriteLine($"Id: {student.Id}, Name: {student.Name}, Age: {student.Age}");
}

Console.WriteLine("Sinh viên trên 20 tuổi (Method Syntax):");
foreach (var student in methodStudents)
{
    Console.WriteLine($"Id: {student.Id}, Name: {student.Name}, Age: {student.Age}");
}
```

**Kết quả**:

```
Sinh viên trên 20 tuổi (Query Syntax):
Id: 2, Name: Bình, Age: 22
Id: 4, Name: Dung, Age: 21
Sinh viên trên 20 tuổi (Method Syntax):
Id: 2, Name: Bình, Age: 22
Id: 4, Name: Dung, Age: 21
```

#### Ví dụ 3: Sử dụng LINQ với GroupBy

```csharp
// Nhóm sinh viên theo độ tuổi
var groupedStudents = from student in students
                     group student by student.Age into ageGroup
                     select new
                     {
                         Age = ageGroup.Key,
                         Students = ageGroup
                     };

// Method Syntax
var groupedStudentsMethod = students
                           .GroupBy(s => s.Age)
                           .Select(g => new { Age = g.Key, Students = g });

Console.WriteLine("Nhóm sinh viên theo tuổi (Query Syntax):");
foreach (var group in groupedStudents)
{
    Console.WriteLine($"Tuổi: {group.Age}");
    foreach (var student in group.Students)
    {
        Console.WriteLine($"  - {student.Name}");
    }
}

Console.WriteLine("Nhóm sinh viên theo tuổi (Method Syntax):");
foreach (var group in groupedStudentsMethod)
{
    Console.WriteLine($"Tuổi: {group.Age}");
    foreach (var student in group.Students)
    {
        Console.WriteLine($"  - {student.Name}");
    }
}
```

**Kết quả**:

```
Nhóm sinh viên theo tuổi (Query Syntax):
Tuổi: 20
  - An
Tuổi: 22
  - Bình
Tuổi: 19
  - Cường
Tuổi: 21
  - Dung
Nhóm sinh viên theo tuổi (Method Syntax):
Tuổi: 20
  - An
Tuổi: 22
  - Bình
Tuổi: 19
  - Cường
Tuổi: 21
  - Dung
```

#### Ví dụ 4: LINQ với Join

```csharp
public class Class
{
    public int ClassId { get; set; }
    public string ClassName { get; set; }
}

List<Class> classes = new List<Class>
{
    new Class { ClassId = 1, ClassName = "Lớp A" },
    new Class { ClassId = 2, ClassName = "Lớp B" }
};

List<Student> studentsWithClass = new List<Student>
{
    new Student { Id = 1, Name = "An", Age = 20, ClassId = 1 },
    new Student { Id = 2, Name = "Bình", Age = 22, ClassId = 2 },
    new Student { Id = 3, Name = "Cường", Age = 19, ClassId = 1 }
};

// Query Syntax
var studentWithClassQuery = from student in studentsWithClass
                           join cls in classes on student.ClassId equals cls.ClassId
                           select new
                           {
                               student.Name,
                               cls.ClassName
                           };

// Method Syntax
var studentWithClassMethod = studentsWithClass
                            .Join(classes,
                                  student => student.ClassId,
                                  cls => cls.ClassId,
                                  (student, cls) => new
                                  {
                                      student.Name,
                                      cls.ClassName
                                  });

Console.WriteLine("Sinh viên và lớp (Query Syntax):");
foreach (var item in studentWithClassQuery)
{
    Console.WriteLine($"Tên: {item.Name}, Lớp: {item.ClassName}");
}

Console.WriteLine("Sinh viên và lớp (Method Syntax):");
foreach (var item in studentWithClassMethod)
{
    Console.WriteLine($"Tên: {item.Name}, Lớp: {item.ClassName}");
}
```

**Kết quả**:

```
Sinh viên và lớp (Query Syntax):
Tên: An, Lớp: Lớp A
Tên: Bình, Lớp: Lớp B
Tên: Cường, Lớp: Lớp A
Sinh viên và lớp (Method Syntax):
Tên: An, Lớp: Lớp A
Tên: Bình, Lớp: Lớp B
Tên: Cường, Lớp: Lớp A
```

### 6. Các phương thức LINQ phổ biến

- **Where**: Lọc dữ liệu theo điều kiện.
- **Select**: Chọn dữ liệu hoặc biến đổi dữ liệu.
- **OrderBy/OrderByDescending**: Sắp xếp dữ liệu tăng/giảm.
- **GroupBy**: Nhóm dữ liệu theo một tiêu chí.
- **Join**: Kết hợp hai tập dữ liệu dựa trên khóa.
- **First/FirstOrDefault**: Lấy phần tử đầu tiên thỏa mãn điều kiện.
- **Count**: Đếm số phần tử.
- **Sum/Min/Max/Average**: Tính toán trên tập dữ liệu.

### 7. Lưu ý khi sử dụng LINQ

- LINQ sử dụng **Deferred Execution** (Thực thi trì hoãn): Truy vấn chỉ được thực thi khi dữ liệu được truy cập (ví dụ: `ToList()`, `foreach`).
- Sử dụng `FirstOrDefault` thay vì `First` để tránh lỗi khi không tìm thấy phần tử.
- LINQ to SQL và LINQ to Entities cần chú ý đến hiệu suất khi làm việc với cơ sở dữ liệu lớn.

### 8. Bài tập thực hành

1. **Bài tập 1**: Tạo một danh sách sản phẩm (`Product`) với các thuộc tính: `Id`, `Name`, `Price`. Sử dụng LINQ để:

   - Lọc các sản phẩm có giá lớn hơn 100.
   - Sắp xếp sản phẩm theo giá giảm dần.
   - Nhóm sản phẩm theo khoảng giá (0-100, 101-200, 201-...).

2. **Bài tập 2**: Tạo hai danh sách: `Orders` (đơn hàng) và `Customers` (khách hàng). Sử dụng LINQ Join để hiển thị thông tin khách hàng và đơn hàng tương ứng.

3. **Bài tập 3**: Sử dụng LINQ để tính tổng, trung bình, giá trị nhỏ nhất và lớn nhất của một danh sách số nguyên.

## Kết luận

- LINQ là công cụ mạnh mẽ để xử lý dữ liệu trong .NET.
- Hiểu rõ cả Query Syntax và Method Syntax để linh hoạt trong các tình huống.
- Thực hành thường xuyên với các ví dụ và bài tập để nắm vững LINQ.
