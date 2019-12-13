## Database, Migrations & Run Backend Application

Change connection string in `appsettings.json` files under `Abp.BookStore.HttpApi.Host` and `Abp.BookStore.DbMigrator` project

```json
{
  "ConnectionStrings": {
    "Default": "Server=(LocalDb)\\MSSQLLocalDB;Database=BookStore;Trusted_Connection=True"
  }
}
```

Open command prompt in `Abp.BookStore.DbMigrator` project and run `dotnet run` to apply Database Migrations

```bash
dotnet run
````

Ensure `Abp.BookStore.HttpApi.Host` project is the `startup project` and `Run Application` which will open `Swagger UI`

[Home](./../../../README.md) | [Previous Step](StepByStep/../../Step1/Step1.md) | [Next Step](StepByStep/../../Step3/Step3.md)
