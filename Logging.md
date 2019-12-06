# Logging

## Add Logging to your service

On an instance of `IServiceCollection` call the `AddLogging()` method.

### Examples
**Console / Web Application**
```csharp
// Program.cs

    public class Program
        {
            public static void Main(string[] args)
            {
                CreateHostBuilder(args).Build().Run();
            }

            public static IHostBuilder CreateHostBuilder(string[] args) =>
                Host.CreateDefaultBuilder(args)
                    .ConfigureServices((hostContext, services) =>
                    {
                        services.AddHostedService<Worker>();

                        services.AddLogging(o =>
                        {
                            o.AddConsole();
                        });
                    });
        }
```

## Using Logging in a Controller or Singleton

Using NetCore's Dependeny Injection you can inject the logger like so in the constructor.

* Prefix the field with an underscore (`_`) to identity that this property was injected.

```csharp
    public class Foo
    {
        private readonly ILogger<Foo> _logger;

        public Foo(ILogger<Foo> logger)
        {
            _logger = logger;
        }

        public void Bar()
        {
            _logger.LogInformation("Bar() has been called.");
            _logger.LogWarning("Oh no, Bar() has been called!");

            // Always include the exception as the first parameter.
            _logger.LogError(new Exception("I Failed, I'm Sorry."), "<What we're you trying to acheive?>")
        }
    }
```
