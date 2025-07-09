# Buổi 20: Sử dụng Repository Pattern

## Mục tiêu
- Hiểu khái niệm Repository Pattern.
- Áp dụng Repository Pattern trong ứng dụng .NET để quản lý dữ liệu.
- Tách biệt logic truy cập dữ liệu khỏi logic nghiệp vụ.
- Tạo mã nguồn dễ bảo trì, dễ mở rộng và kiểm thử.

## Nội dung chính

### 1. Repository Pattern là gì?
- **Định nghĩa**: Repository Pattern là một mẫu thiết kế (design pattern) giúp tách biệt logic truy cập dữ liệu (data access logic) khỏi logic nghiệp vụ (business logic).
- **Mục đích**:
  - Giảm sự phụ thuộc trực tiếp vào cơ chế truy cập dữ liệu (như Entity Framework).
  - Tăng tính tái sử dụng và khả năng kiểm thử (unit test).
  - Đơn giản hóa việc thay đổi công nghệ truy cập dữ liệu (ví dụ: từ SQL Server sang MongoDB).
- **Cơ chế hoạt động**:
  - Repository đóng vai trò trung gian giữa tầng nghiệp vụ và tầng dữ liệu.
  - Cung cấp các phương thức để thực hiện các thao tác CRUD (Create, Read, Update, Delete).
  - Ẩn đi chi tiết triển khai của tầng dữ liệu.

### 2. Cấu trúc của Repository Pattern
- **IRepository (Interface)**: Định nghĩa các phương thức chung cho các thao tác dữ liệu.
- **Concrete Repository**: Lớp cụ thể triển khai interface, chứa logic truy cập dữ liệu.
- **Dependency Injection**: Sử dụng DI để inject repository vào các lớp sử dụng (ví dụ: Controller).

### 3. Ví dụ thực tế
Dự án mẫu: Ứng dụng quản lý danh sách sản phẩm (Product) sử dụng Entity Framework và Repository Pattern.

#### Bước 1: Tạo Model
Tạo class `Product` để đại diện cho đối tượng sản phẩm.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

#### Bước 2: Tạo Interface IRepository
Định nghĩa interface `IProductRepository` với các phương thức CRUD.

```csharp
public interface IProductRepository
{
    IEnumerable<Product> GetAll();
    Product GetById(int id);
    void Add(Product product);
    void Update(Product product);
    void Delete(int id);
}
```

#### Bước 3: Tạo Concrete Repository
Triển khai `ProductRepository` sử dụng Entity Framework.

```csharp
public class ProductRepository : IProductRepository
{
    private readonly AppDbContext _context;

    public ProductRepository(AppDbContext context)
    {
        _context = context;
    }

    public IEnumerable<Product> GetAll()
    {
        return _context.Products.ToList();
    }

    public Product GetById(int id)
    {
        return _context.Products.Find(id);
    }

    public void Add(Product product)
    {
        _context.Products.Add(product);
        _context.SaveChanges();
    }

    public void Update(Product product)
    {
        _context.Products.Update(product);
        _context.SaveChanges();
    }

    public void Delete(int id)
    {
        var product = _context.Products.Find(id);
        if (product != null)
        {
            _context.Products.Remove(product);
            _context.SaveChanges();
        }
    }
}
```

#### Bước 4: Cấu hình Dependency Injection
Cấu hình DI trong `Startup.cs` (hoặc `Program.cs` trong .NET 6+).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<AppDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
    services.AddScoped<IProductRepository, ProductRepository>();
    services.AddControllers();
}
```

#### Bước 5: Sử dụng Repository trong Controller
Tạo `ProductController` để sử dụng repository.

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductController : ControllerBase
{
    private readonly IProductRepository _productRepository;

    public ProductController(IProductRepository productRepository)
    {
        _productRepository = productRepository;
    }

    [HttpGet]
    public IActionResult GetAll()
    {
        var products = _productRepository.GetAll();
        return Ok(products);
    }

    [HttpGet("{id}")]
    public IActionResult GetById(int id)
    {
        var product = _productRepository.GetById(id);
        if (product == null)
            return NotFound();
        return Ok(product);
    }

    [HttpPost]
    public IActionResult Create(Product product)
    {
        _productRepository.Add(product);
        return CreatedAtAction(nameof(GetById), new { id = product.Id }, product);
    }

    [HttpPut("{id}")]
    public IActionResult Update(int id, Product product)
    {
        if (id != product.Id)
            return BadRequest();
        _productRepository.Update(product);
        return NoContent();
    }

    [HttpDelete("{id}")]
    public IActionResult Delete(int id)
    {
        _productRepository.Delete(id);
        return NoContent();
    }
}
```

### 4. Generic Repository Pattern
Để tăng tính tái sử dụng, có thể tạo một `Generic Repository` áp dụng cho nhiều loại entity.

#### Interface Generic Repository
```csharp
public interface IRepository<T> where T : class
{
    IEnumerable<T> GetAll();
    T GetById(int id);
    void Add(T entity);
    void Update(T entity);
    void Delete(int id);
}
```

#### Triển khai Generic Repository
```csharp
public class Repository<T> : IRepository<T> where T : class
{
    private readonly AppDbContext _context;
    private readonly DbSet<T> _dbSet;

    public Repository(AppDbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }

    public IEnumerable<T> GetAll()
    {
        return _dbSet.ToList();
    }

    public T GetById(int id)
    {
        return _dbSet.Find(id);
    }

    public void Add(T entity)
    {
        _dbSet.Add(entity);
        _context.SaveChanges();
    }

    public void Update(T entity)
    {
        _dbSet.Update(entity);
        _context.SaveChanges();
    }

    public void Delete(int id)
    {
        var entity = _dbSet.Find(id);
        if (entity != null)
        {
            _dbSet.Remove(entity);
            _context.SaveChanges();
        }
    }
}
```

#### Sử dụng Generic Repository
Cấu hình DI:

```csharp
services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
```

Sử dụng trong Controller:

```csharp
public class ProductController : ControllerBase
{
    private readonly IRepository<Product> _repository;

    public ProductController(IRepository<Product> repository)
    {
        _repository = repository;
    }

    // Các action tương tự như trên
}
```

### 5. Ưu điểm và nhược điểm của Repository Pattern

#### Ưu điểm
- Tách biệt logic nghiệp vụ và truy cập dữ liệu.
- Dễ dàng thay đổi cơ chế truy cập dữ liệu mà không ảnh hưởng đến tầng nghiệp vụ.
- Tăng tính kiểm thử (dễ mock repository trong unit test).
- Tái sử dụng mã nguồn với Generic Repository.

#### Nhược điểm
- Tăng độ phức tạp của mã nguồn (thêm các lớp và interface).
- Có thể dư thừa trong các ứng dụng nhỏ hoặc đơn giản.
- Nếu không thiết kế tốt, repository có thể trở thành "thùng rác" chứa quá nhiều logic.

### 6. Bài tập ví dụ
1. **Bài tập 1**: Tạo một ứng dụng quản lý danh sách **Category** (Danh mục sản phẩm) sử dụng Repository Pattern.
   - Tạo model `Category` với các thuộc tính: `Id`, `Name`.
   - Tạo `ICategoryRepository` và triển khai `CategoryRepository`.
   - Tạo `CategoryController` để xử lý các yêu cầu CRUD.

2. **Bài tập 2**: Sử dụng Generic Repository để quản lý cả `Product` và `Category`.
   - Cấu hình DI cho Generic Repository.
   - Tạo các controller để sử dụng `IRepository<Product>` và `IRepository<Category>`.

3. **Bài tập 3**: Viết unit test cho `ProductController` sử dụng Moq để mock `IProductRepository`.

### 7. Tài liệu tham khảo
- Video: [Học lập trình .NET - Phần 20 - Sử dụng Repository Pattern](https://www.youtube.com/watch?v=Z7yZ_CPhTdM&list=PLRLJQuuRRcFlaITD5F6XKQJxOt8QgCNAg&index=20)
- Microsoft Docs: [Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/)
- Tài liệu về Dependency Injection: [ASP.NET Core DI](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)