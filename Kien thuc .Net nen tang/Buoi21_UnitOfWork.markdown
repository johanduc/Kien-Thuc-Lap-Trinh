# Buổi 21: Unit of Work

## 1. Giới thiệu về Unit of Work

Unit of Work (UoW) là một mẫu thiết kế (Design Pattern) được sử dụng để quản lý các giao dịch (transactions) và đảm bảo tính nhất quán của dữ liệu khi thực hiện nhiều thao tác với cơ sở dữ liệu. Mục tiêu chính của Unit of Work là:

- **Gộp các thao tác**: Nhóm nhiều thao tác (insert, update, delete) thành một đơn vị công việc duy nhất.
- **Đảm bảo tính nhất quán**: Tất cả các thao tác trong một Unit of Work đều được thực hiện thành công hoặc không thực hiện gì cả (atomicity).
- **Tối ưu hóa hiệu suất**: Giảm số lần giao tiếp với cơ sở dữ liệu bằng cách gộp các thay đổi và thực thi chúng cùng lúc.

Trong .NET, Unit of Work thường được sử dụng cùng với Repository Pattern để quản lý các thao tác với cơ sở dữ liệu thông qua Entity Framework.

## 2. Các thành phần chính của Unit of Work

- **DbContext**: Là trung tâm của Unit of Work, chịu trách nhiệm theo dõi các thay đổi của các thực thể (entities) và thực hiện giao dịch với cơ sở dữ liệu.
- **Repositories**: Các repository cụ thể (ví dụ: `ProductRepository`, `OrderRepository`) được quản lý bởi Unit of Work.
- **Commit**: Phương thức để lưu tất cả các thay đổi vào cơ sở dữ liệu (thường là gọi `SaveChanges` của DbContext).
- **Rollback**: Hủy bỏ các thay đổi nếu có lỗi xảy ra (thường thông qua việc không gọi `SaveChanges` hoặc sử dụng transaction).

## 3. Cấu trúc cơ bản của Unit of Work

Cấu trúc của Unit of Work thường bao gồm:

1. **Interface IUnitOfWork**: Định nghĩa các phương thức và thuộc tính cần thiết.
2. **Lớp UnitOfWork**: Triển khai interface, quản lý DbContext và các repository.
3. **Repositories**: Các lớp repository cụ thể, mỗi lớp chịu trách nhiệm cho một loại entity.

### 3.1. Interface IUnitOfWork

```csharp
public interface IUnitOfWork : IDisposable
{
    IProductRepository ProductRepository { get; }
    IOrderRepository OrderRepository { get; }
    Task<int> CommitAsync();
}
```

### 3.2. Lớp UnitOfWork

```csharp
public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext _context;
    public IProductRepository ProductRepository { get; private set; }
    public IOrderRepository OrderRepository { get; private set; }

    public UnitOfWork(AppDbContext context)
    {
        _context = context;
        ProductRepository = new ProductRepository(_context);
        OrderRepository = new OrderRepository(_context);
    }

    public async Task<int> CommitAsync()
    {
        return await _context.SaveChangesAsync();
    }

    public void Dispose()
    {
        _context.Dispose();
    }
}
```

### 3.3. Repository Pattern

Mỗi repository sẽ chịu trách nhiệm cho một loại entity cụ thể. Ví dụ:

```csharp
public interface IProductRepository
{
    Task<IEnumerable<Product>> GetAllAsync();
    Task<Product> GetByIdAsync(int id);
    Task AddAsync(Product product);
    Task UpdateAsync(Product product);
    Task DeleteAsync(int id);
}

public class ProductRepository : IProductRepository
{
    private readonly AppDbContext _context;

    public ProductRepository(AppDbContext context)
    {
        _context = context;
    }

    public async Task<IEnumerable<Product>> GetAllAsync()
    {
        return await _context.Products.ToListAsync();
    }

    public async Task<Product> GetByIdAsync(int id)
    {
        return await _context.Products.FindAsync(id);
    }

    public async Task AddAsync(Product product)
    {
        await _context.Products.AddAsync(product);
    }

    public async Task UpdateAsync(Product product)
    {
        _context.Products.Update(product);
    }

    public async Task DeleteAsync(int id)
    {
        var product = await _context.Products.FindAsync(id);
        if (product != null)
        {
            _context.Products.Remove(product);
        }
    }
}
```

## 4. Ví dụ sử dụng Unit of Work

Giả sử bạn cần thêm một sản phẩm và một đơn hàng liên quan trong cùng một giao dịch:

```csharp
public async Task CreateOrderWithProductAsync()
{
    using (var unitOfWork = new UnitOfWork(_context))
    {
        // Thêm sản phẩm
        var product = new Product { Name = "Laptop", Price = 1000 };
        await unitOfWork.ProductRepository.AddAsync(product);

        // Thêm đơn hàng
        var order = new Order { ProductId = product.Id, Quantity = 2 };
        await unitOfWork.OrderRepository.AddAsync(order);

        // Lưu tất cả thay đổi
        await unitOfWork.CommitAsync();
    }
}
```

### Giải thích:
- Tất cả các thao tác (thêm sản phẩm và đơn hàng) được thực hiện trong cùng một Unit of Work.
- Chỉ khi gọi `CommitAsync()`, các thay đổi mới được lưu vào cơ sở dữ liệu.
- Nếu có lỗi xảy ra, không gọi `CommitAsync()` sẽ đảm bảo không có thay đổi nào được lưu (rollback).

## 5. Sử dụng Transaction trong Unit of Work

Để đảm bảo tính toàn vẹn dữ liệu, bạn có thể sử dụng giao dịch (transaction) rõ ràng:

```csharp
public async Task CreateOrderWithProductAsync()
{
    using (var unitOfWork = new UnitOfWork(_context))
    {
        using (var transaction = await _context.Database.BeginTransactionAsync())
        {
            try
            {
                // Thêm sản phẩm
                var product = new Product { Name = "Laptop", Price = 1000 };
                await unitOfWork.ProductRepository.AddAsync(product);

                // Thêm đơn hàng
                var order = new Order { ProductId = product.Id, Quantity = 2 };
                await unitOfWork.OrderRepository.AddAsync(order);

                // Lưu thay đổi
                await unitOfWork.CommitAsync();

                // Commit transaction
                await transaction.CommitAsync();
            }
            catch (Exception)
            {
                // Rollback transaction nếu có lỗi
                await transaction.RollbackAsync();
                throw;
            }
        }
    }
}
```

## 6. Bài tập ví dụ

### Bài tập 1: Tạo Unit of Work cho ứng dụng quản lý thư viện
- Tạo một ứng dụng quản lý thư viện với hai entity: `Book` và `BorrowRecord`.
- Tạo interface và triển khai `IUnitOfWork` để quản lý `BookRepository` và `BorrowRecordRepository`.
- Viết một phương thức để thêm một cuốn sách và một bản ghi mượn sách trong cùng một giao dịch.

#### Giải pháp:

```csharp
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Author { get; set; }
}

public class BorrowRecord
{
    public int Id { get; set; }
    public int BookId { get; set; }
    public string BorrowerName { get; set; }
    public DateTime BorrowDate { get; set; }
}

public interface IBookRepository
{
    Task AddAsync(Book book);
}

public interface IBorrowRecordRepository
{
    Task AddAsync(BorrowRecord record);
}

public class BookRepository : IBookRepository
{
    private readonly AppDbContext _context;

    public BookRepository(AppDbContext context)
    {
        _context = context;
    }

    public async Task AddAsync(Book book)
    {
        await _context.Books.AddAsync(book);
    }
}

public class BorrowRecordRepository : IBorrowRecordRepository
{
    private readonly AppDbContext _context;

    public BorrowRecordRepository(AppDbContext context)
    {
        _context = context;
    }

    public async Task AddAsync(BorrowRecord record)
    {
        await _context.BorrowRecords.AddAsync(record);
    }
}

public interface IUnitOfWork : IDisposable
{
    IBookRepository BookRepository { get; }
    IBorrowRecordRepository BorrowRecordRepository { get; }
    Task<int> CommitAsync();
}

public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext _context;
    public IBookRepository BookRepository { get; private set; }
    public IBorrowRecordRepository BorrowRecordRepository { get; private set; }

    public UnitOfWork(AppDbContext context)
    {
        _context = context;
        BookRepository = new BookRepository(_context);
        BorrowRecordRepository = new BorrowRecordRepository(_context);
    }

    public async Task<int> CommitAsync()
    {
        return await _context.SaveChangesAsync();
    }

    public void Dispose()
    {
        _context.Dispose();
    }
}

public async Task AddBookAndBorrowRecordAsync()
{
    using (var unitOfWork = new UnitOfWork(_context))
    {
        using (var transaction = await _context.Database.BeginTransactionAsync())
        {
            try
            {
                // Thêm sách
                var book = new Book { Title = "Clean Code", Author = "Robert C. Martin" };
                await unitOfWork.BookRepository.AddAsync(book);

                // Thêm bản ghi mượn
                var borrowRecord = new BorrowRecord
                {
                    BookId = book.Id,
                    BorrowerName = "John Doe",
                    BorrowDate = DateTime.Now
                };
                await unitOfWork.BorrowRecordRepository.AddAsync(borrowRecord);

                // Lưu thay đổi
                await unitOfWork.CommitAsync();

                // Commit transaction
                await transaction.CommitAsync();
            }
            catch (Exception)
            {
                // Rollback nếu có lỗi
                await transaction.RollbackAsync();
                throw;
            }
        }
    }
}
```

### Bài tập 2: Mở rộng Unit of Work
- Thêm phương thức `RollbackAsync` vào `IUnitOfWork` và triển khai để hủy bỏ các thay đổi.
- Tích hợp kiểm tra lỗi (validation) trước khi commit.

#### Giải pháp:

```csharp
public interface IUnitOfWork : IDisposable
{
    IBookRepository BookRepository { get; }
    IBorrowRecordRepository BorrowRecordRepository { get; }
    Task<int> CommitAsync();
    Task RollbackAsync();
}

public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext _context;
    public IBookRepository BookRepository { get; private set; }
    public IBorrowRecordRepository BorrowRecordRepository { get; private set; }

    public UnitOfWork(AppDbContext context)
    {
        _context = context;
        BookRepository = new BookRepository(_context);
        BorrowRecordRepository = new BorrowRecordRepository(_context);
    }

    public async Task<int> CommitAsync()
    {
        // Kiểm tra validation (giả sử có logic kiểm tra)
        if (!IsValid())
        {
            throw new InvalidOperationException("Dữ liệu không hợp lệ");
        }

        return await _context.SaveChangesAsync();
    }

    public async Task RollbackAsync()
    {
        // Xóa tất cả các thay đổi đang được theo dõi
        _context.ChangeTracker.Clear();
    }

    private bool IsValid()
    {
        // Logic kiểm tra dữ liệu
        return true; // Giả sử dữ liệu hợp lệ
    }

    public void Dispose()
    {
        _context.Dispose();
    }
}
```

## 7. Lợi ích và hạn chế của Unit of Work

### Lợi ích:
- **Tính nhất quán**: Đảm bảo tất cả các thao tác được thực hiện thành công hoặc không thực hiện gì cả.
- **Tái sử dụng**: Có thể sử dụng lại các repository trong nhiều Unit of Work.
- **Dễ bảo trì**: Tách biệt logic nghiệp vụ và logic truy cập dữ liệu.

### Hạn chế:
- **Phức tạp**: Tăng độ phức tạp của mã nguồn, đặc biệt với các ứng dụng nhỏ.
- **Hiệu suất**: Nếu không quản lý tốt, có thể dẫn đến việc theo dõi quá nhiều thay đổi trong DbContext, gây chậm trễ.
- **Khả năng mở rộng**: Khi số lượng repository tăng lên, việc quản lý trong Unit of Work có thể trở nên cồng kềnh.

## 8. Kết luận

Unit of Work là một mẫu thiết kế mạnh mẽ để quản lý các giao dịch trong ứng dụng .NET, đặc biệt khi sử dụng Entity Framework. Kết hợp với Repository Pattern, nó giúp mã nguồn trở nên rõ ràng, dễ bảo trì và đảm bảo tính toàn vẹn dữ liệu. Tuy nhiên, cần cân nhắc sử dụng trong các dự án phù hợp để tránh tăng độ phức tạp không cần thiết.