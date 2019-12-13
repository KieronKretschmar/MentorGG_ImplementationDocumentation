# Databases

## Providers
We choose MySQL as the database provider whereever suitable.

## .NET Core projects 

We create databases using the [EF Code First][EFCF] approach with [Code First Migrations][CFM] whereever suitable.

### Setting up a new database
1. Create a Class Library (.NET Standard) named `Entities` and place the model definitions inside `Entities.Models`. [Start from scratch][EFCF] or use EFCore's `scaffold` command with an existing database.

2. Create a Class Library (.NET Standard) named `Database` for the database context. 
Add References to `Entities`, `EFCore3.0`, `EFCore.Tools` and the EFCore database driver<sup>[1](#pomeloFootnote)</sup>.

3. [Create a Context][EFCFCC] class that's inheriting from `DbContext`. 
Add a DbSet property for each Entity, e.g. `public virtual DbSet<Match> Matches { get; set; }`

4. If you want to provide REST endpoints for the database, create a ASP.NET Core Web Application named `<name>DBI`. Add reference to `Database` and `EFCore.Design`.

5. (Optional) To access the ConnectionString as an environment variable in development and production, follow these steps for the project from the previous step:
    - Add the environment variable with Rightclick on Project -> Properties -> Debug -> Environment Variables `Add`
    - In Startup.cs call `.AddEnvironmentVariables()` like this
        ```csharp
        public Startup(IConfiguration configuration)
        {
            Configuration = new ConfigurationBuilder()
                .AddConfiguration(configuration)
                .AddEnvironmentVariables()
                .Build();
        }
        ```

6. In the project from step 3, add the database connection to services. For testing, it might be useful to use an InMemory database if no ConnectionString is provided. Configuration in Startup.cs may look like this:
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // AddControllers and Logging

        // if a connectionString is set use mysql, else use InMemory
        var connString = Configuration.GetValue<string>("MYSQL_CONNECTION_STRING");
        if (connString != null)
        {
            services.AddDbContext<Database.MatchContext>(o => { o.UseMySql(connString); });
        }
        else
        {
            services.AddEntityFrameworkInMemoryDatabase()
                .AddDbContext<Database.MatchContext>((sp, options) =>
                {
                    options.UseInMemoryDatabase(databaseName: "MyInMemoryDatabase").UseInternalServiceProvider(sp);
                });
        }
    }
    ``` 

6. If you skipped steps 3 - 5, add a Project with a .NET Core or .NET Framework runtime, e.g. a Console App (.NET Core), that references `Database` and `EFCore.Design`. This is required to configure migrations.


For more information see this [Guide](https://garywoodfine.com/using-ef-core-in-a-separate-class-library-project/)

### Modifying the database
To make changes to the database schema follow these steps:
1. Apply changes of the model definitions to Entities.Models and other database changes (e.g. adding or dropping ForeignKeys / Indexes) to Database.
2. In order to add a new migration, select any project that references `Database`, `EFCore.Design` and supplies a .NET Core runtime as StartUp Project.
3. Open the Package Manager with `Database` as the target project. If required, set the ConnectionString as an environment variable like 

    `PM> $env:MYSQL_CONNECTION_STRING="server=..."`. 

4. Run `add-migration <migration name>` in Package Manager. 
If you receive an error that the ConnectionString from the previous step could not be read or "Unable to resolve service for type 'Microsoft.EntityFrameworkCore.Migrations.IMigrator'",
try setting it again (setting environment variables in PM might be a bit buggy, not sure).
5. Modify the `Up()` and `Down()` method in the newly created migration in Database if necessary.
6. Run `update-database` in Package Manager to update your local database.




[EFCF]: https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/workflows/new-database
[CFM]: https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/
[EFCFCC]: https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/workflows/new-database#3-create-a-context
<a name="pomeloFootnote">1</a>: 
As of 12.11.2019 we are using Pomelo.EntityFrameworkCore.MySql v3.0.0-rc3 as MySql.Data.EntityFrameworkCore v8.0.18 and older versions of Pomelo.EntityFrameworkCore.MySql are not compatible with EFCore 3.0.
Install with `Install-Package Pomelo.EntityFrameworkCore.MySql -Version 3.0.0-rc3.final`
See [StackOverflow](https://stackoverflow.com/questions/57836886/configure-mysql-with-efcore-3-0)
