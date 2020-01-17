# Databases

## Providers
We choose MySQL as the database provider whereever suitable.

## .NET Core projects 

We create databases using the [EF Code First][EFCF] approach with [Code First Migrations][CFM] whereever suitable.

### Setting up a new database
1. Setup a project `Entities` for the models to be stored in the database. Usually it's one model per table. 

- Create a Class Library (.NET Standard 2.1) named `Entities`.
- Add classes for each model. [Start from scratch][EFCF] or use EntityFrameworkCore's `scaffold` command with an existing database. The recommended namespace is  `Entities.Models`.

2. Setup a project `Database` for configuring the database.
- Create a Class Library (.NET Standard 2.1) named `Database` for the database context. 
- Add References to `Entities`, `EntityFrameworkCore3.0`, `EntityFrameworkCore.Tools`.
- Add a reference to the EntityFrameworkCore database driver<sup>[1](#pomeloFootnote)</sup>.
- [Create a Context][EFCFCC] class that's inheriting from `DbContext`. 
- Add a DbSet property to the Context for each Entity, e.g. `public virtual DbSet<Match> Matches { get; set; }`.

3. Apply changes to your application project of this solution (e.g. an ASP.NET Core Web Application or a Console App (EF Core)).
- Add references to `Database` and `EntityFrameworkCore.Design`.
- Add an environment variable for the connectionstring with Rightclick on Project -> Properties -> Debug -> Environment Variables `Add`.
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
- Add the database connection to services. For testing, it might be useful to use an InMemory database if no ConnectionString is provided. Configuration in Startup.cs may look like this:
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // AddControllers and Logging

        // Try to read connectionstring from environment variable
        var connString = Configuration.GetValue<string>("MYSQL_CONNECTION_STRING");
        
        // if a connectionString is set use mysql, else use InMemory
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

4. If you skipped step 3 but still want to configure migrations, you need to create a new project to provide a runtime environment for creating migrations
- Add a Project with a .NET Core or .NET Framework runtime, e.g. a Console App (.NET Core).
- Add references to `Database` and `EntityFrameworkCore.Design`.

You are now ready to create your first migration!

For more information on setting up a new database see this [Guide](https://garywoodfine.com/using-ef-core-in-a-separate-class-library-project/)

### Modifying the database and creating migrations
To make changes to the database schema follow these steps:
1. Apply changes of the model definitions to Entities.Models and other database changes (e.g. adding or dropping ForeignKeys / Indexes) to the context in Database.
2. In order to add a new migration, select any project that references `Database`, `EntityFrameworkCore.Design` and supplies a .NET Core runtime as StartUp Project.
3. Open the Package Manager with `Database` as the target project. If required, set the ConnectionString as an environment variable like 

    `PM> $env:MYSQL_CONNECTION_STRING="server=..."`. 

4. Run `add-migration <migration name>` in Package Manager. 
If you receive an error that the ConnectionString from the previous step could not be read or "Unable to resolve service for type 'Microsoft.EntityFrameworkCore.Migrations.IMigrator'",
try setting it again (setting environment variables in PM might be a bit buggy, not sure).
5. Modify the `Up()` and `Down()` method in the newly created migration in Database if necessary.
6. Run `update-database` in Package Manager to update your local database.

### Conventions
#### Fluent API
We are using the [Fluent API][Fluent API Docs] where possible to configure the model, avoiding the usage of attributes for database configuration in `Entities`.


[Fluent API Docs]: https://www.learnentityframeworkcore.com/configuration/fluent-api
[EFCF]: https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/workflows/new-database
[CFM]: https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/
[EFCFCC]: https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/workflows/new-database#3-create-a-context
<a name="pomeloFootnote">1</a>: 
As of 12.11.2019 we are using Pomelo.EntityFrameworkCore.MySql v3.0.0-rc3 as MySql.Data.EntityFrameworkCore v8.0.18 and older versions of Pomelo.EntityFrameworkCore.MySql are not compatible with EntityFrameworkCore 3.0.
Install with `Install-Package Pomelo.EntityFrameworkCore.MySql -Version 3.0.0-rc3.final`
See [StackOverflow](https://stackoverflow.com/questions/57836886/configure-mysql-with-EntityFrameworkCore-3-0)
