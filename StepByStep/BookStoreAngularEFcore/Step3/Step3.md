## Book Entity & Migrations

### Book

Add `Book` class to domain layer `Abp.BookStore.Domain` project

```csharp
public class Book : AuditedAggregateRoot<Guid>
{
    public string Name { get; set; }

    public BookType Type { get; set; }

    public DateTime PublishDate { get; set; }

    public float Price { get; set; }
}
```

Define `BookType` enum in the `Abp.BookStore.Domain.Shared`

```csharp
public enum BookType
{
    Undefined,
    Adventure,
    Biography,
    Dystopia,
    Fantastic,
    Horror,
    Science,
    ScienceFiction,
    Poetry
}
```

Add `DbSet<Book>` to DbContext in `Abp.BookStore.EntityFrameworkCore`

```csharp
public class BookStoreDbContext : AbpDbContext<BookStoreDbContext>
{
    public DbSet<Book> Books { get; set; }
...
}
```

Open `BookStoreDbContextModelCreatingExtensions.cs` in `Abp.BookStore.EntityFrameworkCore` project and add following code to the end of the ConfigureBookStore method to configure the Book entity

```csharp
builder.Entity<Book>(b =>
{
    b.ToTable(BookStoreConsts.DbTablePrefix + "Books", BookStoreConsts.DbSchema);
    b.ConfigureByConvention(); //auto configure for the base class props
    b.Property(x => x.Name).IsRequired().HasMaxLength(128);
});
```

Copy Paste `appsettings.json` from `Abp.BookStore.HttpApi.Host` to `Abp.BookStore.EntityFrameworkCore.DbMigrations`

Open command prompt in `Abp.BookStore.EntityFrameworkCore.DbMigrations` and run:

```bash
dotnet ef migrations add "book_entity"
dotnet ef database update
```

[Home](./../../../README.md) | [Previous Step](StepByStep/../../Step2/Step2.md) | [Next Step](StepByStep/../../Step4/Step4.md)
