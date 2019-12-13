## Seed Sample Data

Add a `BookStoreDataSeedContributor` class deriving from `IDataSeedContributor` to  `Bvh.BookStore.Domain`

```csharp
public class BookStoreDataSeedContributor: IDataSeedContributor, ITransientDependency
{
    private readonly IRepository<Book, Guid> _bookRepository;

    public BookStoreDataSeedContributor(IRepository<Book,Guid> bookRepository) => _bookRepository = bookRepository;

    public async Task SeedAsync(DataSeedContext context)
    {
        if (await _bookRepository.GetCountAsync() > 0)
        {
            return;
        }

        await _bookRepository.InsertAsync(
            new Book
            {
                Name = "1984",
                Type = BookType.Dystopia,
                PublishDate = new DateTime(1949, 6, 8),
                Price = 19.84f
            }
        );

        await _bookRepository.InsertAsync(
            new Book
            {
                Name = "The Hitchhiker's Guide to the Galaxy",
                Type = BookType.ScienceFiction,
                PublishDate = new DateTime(1995, 9, 27),
                Price = 42.0f
            }
        );
    }
}
```

Open a command prompt in `Bvh.BookStore.DbMigrator` and run following command to seed sample data

`dotnet run`

Check `BookStore` Database to see Inserted Data

[Home](./../../../README.md) | [Previous Step](StepByStep/../../Step3/Step3.md) | [Next Step](StepByStep/../../Step5/Step5.md)