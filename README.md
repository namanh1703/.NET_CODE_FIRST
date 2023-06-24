#.NET_CODE_FIRST

# :notebook_with_decorative_cover: Table of Contents
- [Lý Thuyết](#Lý-Thuyết)
- [Quy ước](#Quy-ước)
- [Hưỡng dẫn tạo database](#Hưỡng-dẫn-tạo-database)
- [Hưỡng dẫn thêm dữ liệu vào database](#Hưỡng-dẫn-thêm-dữ-liệu-vào-database)

## Lý Thuyết

- `Code-First` có nghĩa là trước tiên bạn cần bắt đầu viết mã bằng C# sau đó Entity Framework sẽ tạo cơ sở dữ liệu từ mã của bạn.

- Trong phương pháp Code-First, bạn tập trung vào miền ứng dụng của mình và bắt đầu tạo các lớp cho thực thể miền thay vì thiết kế cơ sở dữ liệu của bạn trước rồi tạo các lớp phù hợp với thiết kế cơ sở dữ liệu của bạn.

![image](https://user-images.githubusercontent.com/85175337/226838387-f12fe5dd-d3f9-4c81-a60e-18d6a5d36331.png)

- Quy trình làm việc Code-First

![image](https://user-images.githubusercontent.com/85175337/226838477-a8856862-3997-4d24-b4e6-e94255ec7d30.png)

![image](https://user-images.githubusercontent.com/85175337/226842646-516ca1a2-0164-4cdf-8068-1bae368da6a0.png)

## Quy ước

- `Table Name`: <Tên class> + 's'. Ví dụ class Student thì khi đặt tên ánh xạ sang DB thì nên đặt là Students 

- `Primary key Name`:  1 là để Id hoặc <Tên class> +"Id" điều đó là bắt buộc để khi biên dịch EF mới biết đâu là khóa chính. Nếu mà muốn đặt tên khóa chính khác với 2 cái trên thì thêm đánh dấu [Key] ở trước 

![image](https://user-images.githubusercontent.com/85175337/226841274-e07edf4b-7b39-4738-a1b1-30ccbf2edf40.png)

Trường hợp đánh 2 cặp khóa cho bảng trung gian để hỗ trợ cho mỗi quan hệ n-n thì có thể dùng PrimaryKey

![image](https://user-images.githubusercontent.com/85175337/226841597-765fe11b-5571-4761-b660-4fef434479e9.png)

![image](https://user-images.githubusercontent.com/85175337/226841685-9616a1f3-ea5e-4e6e-95f8-24c4b776978e.png)

- `Foreign key property Name`: 

![image](https://user-images.githubusercontent.com/85175337/226843405-e879ad25-7653-4e59-829d-4c098ed7ce33.png)

- `Null column`: EF tạo 1 cột có thể rỗng

- `DB Columns order`: Thứ tự cột sẽ được sinh ra trong table đó

![image](https://user-images.githubusercontent.com/85175337/226844958-2f37de99-d6ca-4023-a423-27f59fee95ee.png)


- `Kiểu dữ liệu khi chuyển sang`:

![image](https://user-images.githubusercontent.com/85175337/226842770-1b03ea57-6a2b-4f79-adb3-1fba14f8a969.png)

- `Cascade delete`: Tự động xóa các dữ liệu con



## Hưỡng dẫn tạo database

Lưu ý add đủ thư viện

```C#
Microsoft.EntityFrameworkCore
```

```C#
Microsoft.EntityFrameworkCore.SqlServer
```

```C#
Microsoft.EntityFrameworkCore.Tools
```

![image](https://user-images.githubusercontent.com/85175337/227417718-803088ea-0485-4444-8959-5bbee70403bc.png)

Trước khi chạy câu lệch phải có class Context và kế thừa DBContext 

Lưu ý: nhớ đổi tên database phù hợp và nó sẽ `tạo tên database tương ứng với tên bạn đặt `

```C#
 public class JustBlogContext : DbContext
    {
        public DbSet<Category> Categories { get; set; }
        public DbSet<Post> Posts { get; set; }
        public DbSet<Tag> Tags { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer("server=.;database=DBJustBlog;Trusted_Connection=True;TrustServerCertificate=True");
        }
    }
```

- Vào đúng Package Manager Console để chạy câu lệch: 

![image](https://user-images.githubusercontent.com/85175337/227417864-a81074a9-1dc9-44bf-be5c-81bea6b55cca.png)

- Tạo Migration `add-migration` + với tên Database ( nó không liên quan gì đến việc đặt tên database )

```C#
add-migration DBJustBlog
```

- Cập nhập xuống database

```C#
update-database
```
Sau khi chạy hoàn thiện sẽ được folder Migrations

![image](https://user-images.githubusercontent.com/85175337/227419420-906f3e42-335d-4889-b11c-05f5558572cd.png)

## Hưỡng dẫn thêm dữ liệu vào database
- Tạo 1 class static để thêm phương thức hàm cho ModelBuilder 

```C#
public static class JustBlogStore
    {
        public static void SeedData(this ModelBuilder builder)
        {
            // add data 

            builder.Entity<Category>().HasData(
                new Category
                {
                    Id = 1,
                    Name = "PRO",
                    UrlSlug = "abc.com",
                    Description = "abc"
                }
                );
        }
    }
```

- kế thừa hàm của DbContext OnModelCreating để cấu hình thêm dữ liệu

```C#
public class JustBlogContext : DbContext
    {
        public DbSet<Category> Categories { get; set; }
        public DbSet<Post> Posts { get; set; }
        public DbSet<Tag> Tags { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer("server=.;database=DBJustBlog;Trusted_Connection=True;TrustServerCertificate=True");
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);
            modelBuilder.SeedData();
        }
    }
```

Sau khi thêm xong thì chạy lại câu lệch `add-migration DBJustBlog1` và `update-database`

Lưu ý mỗi lần add dữ liệu thì nên đặt tên migration khác nhau để tránh lỗi ví dự

Lần 1 là `add-migration DBJustBlog1` thì lần 2 phải khác tên lần 1 thì `add-migration DBJustBlog3` hoặc có thể trực tiếp xóa file Migrations



