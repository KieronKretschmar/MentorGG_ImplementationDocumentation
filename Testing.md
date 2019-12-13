# Testing with .NET Core

## Adding a Test Project to a solution
In order to write tests add a MSTest Test Project to the solution and give it a name that ends with "Tests", following the [Docs]("https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-mstest")

## Testing and Dependency Injection
Some tests require services that are usually injected as dependencies, and/or require injected dependencies themselves. 
Outlined below are two ways to get these services in a `TestClass`.

### Get service from a ServiceCollection
By creating a ServiceCollection we can access services just like in production code. To achieve this, follow these steps:

first create a constructor for the `Testclass`. In it, create a `ServiceCollection` with all required dependencies for your test. Make sure to also include their dependencies. Then create a ServiceProvider and call .GetService<>() on it to obtain the services you need for testing.

Here's an example of a TestClasses constructor:
```csharp
public DemoHandlerTest()
{
    var services = new ServiceCollection();
    
    // Even though we only need DemoHandler in this TestClass, we need to add all the services required by DemoHandler as well.
    services.AddLogging(o =>
    {
        o.AddConsole();
        o.AddDebug();
    });
    services.AddTransient<IAnalyzerWorker, AnalyzerWorker>();
    services.AddTransient<IUnzipper, Unzipper>();
    services.AddSingleton<IDuplicateChecker, DuplicateChecker>();
    services.AddSingleton<IEquipmentProvider, EquipmentProvider>();
    services.AddTransient<IDemoHandler, DemoHandler>();

    var serviceProvider = services.BuildServiceProvider();

    // Get a service like this
    _demoHandler = serviceProvider.GetService<DemoHandler>();
}
```	

### Mocking dependencies
Some dependencies are not available during testing. For these, we use [Moq](MOQ) to create Mockup classes that implement the same interface  add that to the ServiceCollection instead of the real service. Here's an example:
```csharp
[TestMethod]
public async Task FailedUserCreationTest()
{
    // Setup mockFaceitApiCommunicator
    var mockFaceitApiCommunicator = new Mock<IFaceitApiCommunicator>();

    // Setup mockFaceitOAuthCommunicator and make its CreateUser() method throw a FaceitFailedUserCreationException
    var mockFaceitOAuthCommunicator = new Mock<IFaceitOAuthCommunicator>();
    mockFaceitOAuthCommunicator
        .Setup(x => x.CreateUser(It.IsAny<long>(), It.IsAny<string>()))
        .Throws(new FaceitOAuthCommunicator.FaceitFailedUserCreationException(""));

    // Create UsersController with mocked dependencies
    var usersController = new UsersController(mockFaceitOAuthCommunicator.Object, mockFaceitApiCommunicator.Object);

    // Call endpoint with invalid code
    var result = await usersController.PostUser(1, "invalidCode");

    // Check for expected behavior
    Assert.IsInstanceOfType(result, typeof(Microsoft.AspNetCore.Mvc.BadRequestResult));
    
}
```

## Testing and Environment variables
Setting up environment variables in launchSettings.json is useful during development (when running F5 in Visual Studio), but these can not be accessed by test projects. You can debug tests locally by setting the required environment variables using the Package Manager Console by running `PM> $env:MY_ENV_VARIABLE="value"`. Warning: This only works when running the tests with rightclick->Debug, not with rightclick->Run.


## Testing and databases
Tests should be run against InMemory databases, one fresh database for each testmethod. The constructor of the testclass does not need to be modified. Create and use it inside test methods like this.:

```csharp
[TestMethod]
public void MyTestMethod()
{
    // Create options object with insrtuctions to use InMemoryDatabase
    var options = new DbContextOptionsBuilder<DatabaseContext>()
        .UseInMemoryDatabase(databaseName: "MyTestMethodDatabase")
        .Options;

    // Do stuff with the database e.g. inject it into the module that is tested 
    using (var context = new DatabaseContext(options))
    {
        // Do stuff with the database
    }


    // Use a separate instance of the context to verify correct data was saved to database
    using (var context = new DatabaseContext(options))
    {
        // Perform checks
    }
}
```


[MOQ]: (https://github.com/Moq/moq4/wiki/Quickstart)
