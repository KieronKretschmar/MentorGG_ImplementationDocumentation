# API Documentation

When creating a REST API service, documentation is required.

## Tools

- [Swagger](https://swagger.io/)
- [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)

## Adding Swagger to your service

Install the Nuget package.

```shell
$ Install-Package Swashbuckle.AspNetCore -Version 5.0.0
```

Optionally, install the annotations package - This provides the ability to add custom attributes to your endpoints.

```shell
$ Install-Package Swashbuckle.AspNetCore.Annotations -Version 5.0.0
```

Add Swagger in `Startup.cs`

```csharp
public virtual void ConfigureServices(IServiceCollection services)
{
	[...]

	#region Swagger
        services.AddSwaggerGen(options =>
        {
            OpenApiInfo interface_info = new OpenApiInfo { Title = "[SERVICE NAME]", Version = "v1", };
            options.SwaggerDoc("v1", interface_info);

            // Generate documentation based on the XML Comments provided.
            var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
            var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
            options.IncludeXmlComments(xmlPath);

	    // Optionally, if installed, enable annotations
            options.EnableAnnotations();
        });
        #endregion
	
	[...]

}
```

```csharp
public virtual void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
	[...]

        #region Swagger
        app.UseSwagger();
	app.UseSwaggerUI(options =>
        {
            options.RoutePrefix = "swagger";
            options.SwaggerEndpoint("/swagger/v1/swagger.json", "[SERVICE NAME]");
        });
        #endregion	

	[...]
}
```

Finally,

- Ensure **every** REST endpoint it documented with XML comments

- [Enrich XML Comments](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#include-descriptions-from-xml-comments)

- Follow these [naming guidelines](https://restfulapi.net/resource-naming/)












