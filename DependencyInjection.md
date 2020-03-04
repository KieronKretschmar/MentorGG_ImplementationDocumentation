# Dependency Injection

**Dependency injection** is a technique whereby one object supplies the dependencies of another object. As opposed to the other object creating its dependencies. 

We are using constructor injection. 
```csharp
private readonly IDependencyA _dependencyA;
private readonly IDependencyB _dependencyB;

// We are injecting Interfaces of our dependencies in the constructor
public DependingService(IDependencyA dependencyA,IDependencyB dependencyB)
        {
            _dependencyA = dependencyA;
            _dependencyB = dependencyB;
        }

```

This way a `DependingService` can not be created without its dependencies.

## ASP.NET Core

ASP.NET Core provides a dependency injection framework. 
Inside the `startup.ConfigureServices()` is a `IServiceCollection services`, to which services can be added. These services are then automatically resolved when 
requested as dependencies. 

```csharp
public void ConfigureServices(IServiceCollection services) 
{
	services.AddTransient<DependingService>();
	services.AddScoped<IDependencyA, DependencyA>(); 
	services.AddSingleton<IDependencyB>(); 
}
```

All these methods are extension methods provided by ASP.NET.
Despite following the same scheme of `services.Add{Lifetime}`, there are different ways to use these methods.

```csharp
//services.Add{LIFETIME}<{{IMPLEMENTATION}>();
services.AddSingleton<DependencyA>();

//services.Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()
//Allows you to register multiple implementations for the same interface
services.AddSingleton<IDependencyA, DependencyA>();


//services.Add{LIFETIME}<{SERVICE},{IMPLEMENTATION}>(sp => new {IMPLEMENTATION});
//Allows you to register multiple implementation and pass arguments
services.AddSingleton<IDependencyA,DependencyA>(sp => 
{
	return new DependencyA("dependencyArgs");
});


//DO NOT USE THIS WAY
//It does not include automatic disposal of services
services.AddSingleton(new MyDep());
```
 ## Service Life Times
 There are three lifetimes in our DI system:
 

 1. **Transient** services are created every time they are injected or requested.
 2. **Scoped** services are created per scope
 3. **Singleton** services are created per DI container.

Services are disposed of when their lifetimes end.  A new instance has to be created afterwards.
In general, Register your services as **transient** wherever possible.
Do not depend on these services from a **singleton** instance.

