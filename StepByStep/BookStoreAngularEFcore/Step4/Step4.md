## Seed Sample Data

Add a `BookStoreDataSeedContributor` class deriving from `IDataSeedContributor` to  `Abp.BookStore.Domain`

```csharp
public class BookStoreDataSeedContributor: IDataSeedContributor, ITransientDependency
{
    private readonly IRepository<Book, Guid> _bookRepository;
    private readonly IGuidGenerator _guidGenerator;

    public BookStoreDataSeedContributor(IRepository<Book,Guid> bookRepository, IGuidGenerator guidGenerator)
    {
        _bookRepository = bookRepository;
        _guidGenerator = guidGenerator;
    }

    public async Task SeedAsync(DataSeedContext context)
    {
        if (await _bookRepository.GetCountAsync() > 0)
        {
            return;
        }

        await _bookRepository.InsertAsync(
            new Book(_guidGenerator.Create(), "Test book 1", BookType.Fantastic, new DateTime(2015, 05, 24), 21)
        );

        await _bookRepository.InsertAsync(
            new Book(_guidGenerator.Create(), "Test book 2", BookType.Science, new DateTime(2014, 02, 11), 15)
        );
    }
}
```

Open a command prompt in `Abp.BookStore.DbMigrator` and run following command to seed sample data

```bash
dotnet run
```

Check the `BookStore` Database in `Microsoft SQL Server Management Studio` to see Inserted Data

[Home](./../../../README.md) | [Previous Step](StepByStep/../../Step3/Step3.md) | [Next Step](StepByStep/../../Step5/Step5.md)
