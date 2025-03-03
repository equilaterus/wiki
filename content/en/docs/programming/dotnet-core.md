---
title: ".Net Core"
description: "Quick-starts and guidelines for .Net Core development."
lead: "Quick-starts and guidelines for .Net Core development"
date: 2024-23-04
lastmod: 2024-23-04
draft: false
images: []
menu:
  docs:
    parent: "programming"
weight: 10
toc: true
---


## Sample Projects

* .NET 8.0 [gamestoolkit.api](https://github.com/gamestoolkit/gamestoolkit.api)

  Clean Architecture / Dapper / EFCore / CQRS / AutoMapper / Swagger / Functional testing / Unit testing / CI.

* .NET Core 2.2 [AspCore.SampleAPI](https://github.com/dacanizares/AspCore.SampleAPI)

  Clean Architecture / Domain / Behaviors / Queries / AutoMapper / Swagger / Functional testing / CI.

## Quick guides/samples

{{< alert icon="💡" text="Hotspots and cheatsheets for your common use cases." />}}



### Web API Example

* Build web APIs with [.NET 8.0](https://docs.microsoft.com/en-us/aspnet/core/web-api/?view=aspnetcore-8.0)
  * Tutorial - Controllers [.NET 8.0](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-8.0)
  * Tutorial: Minimal API [.NET 8.0](https://learn.microsoft.com/en-us/aspnet/core/tutorials/min-web-api?view=aspnetcore-8.0)
* Action return types [.NET 8.0](https://learn.microsoft.com/en-us/aspnet/core/web-api/action-return-types?view=aspnetcore-8.0)

```csharp
[Route("api/[controller]")]
[ApiController]
public class UsersController : ControllerBase
{
    protected readonly IUsersRepository _repository;

    public UsersController(IUsersRepository repository)
    {
        _repository = repository;
    }

    [HttpGet]
    [ProducesResponseType(200)]
    public async Task<ActionResult<IEnumerable<User>>> GetAllAsync()
    {
        return await _repository.GetAll();
    }

    [HttpGet("{username}")]
    [ProducesResponseType(200)]
    [ProducesResponseType(404)]
    public async Task<ActionResult<User>> GetByIdAsync(string username)
    {
        var existingUser = await _repository.GetByUsername(username);
        if (existingUser == null)
        {
            return NotFound();
        }
        return existingUser;
    }

    [HttpPost]
    [ProducesResponseType(201)]
    [ProducesResponseType(400)]
    public async Task<ActionResult<User>> CreateUserAsync(User user)
    {
        await _repository.AddUser(user);
        return CreatedAtAction(nameof(GetById), new { username = user.Username }, user);
    }

    [HttpPut("{username}")]
    [ProducesResponseType(204)]
    [ProducesResponseType(404)]
    public async Task<IActionResult> UpdateUserAsync(string username, User user)
    {
        var existingUser = await _repository.GetByUsername(username);
        if (existingUser == null)
        {
            return NotFound();
        }

        existingUser.Nickname = user.Nickname;
        existingUser.Email = user.Email;

        await _repository.Update(existingUser);
        return NoContent();
    }

    [HttpDelete("{username}")]
    [ProducesResponseType(204)]
    [ProducesResponseType(404)]
    public async Task<IActionResult> DeleteUserAsync(string username)
    {
        var existingUser = await _repository.GetByUsername(username);
        if (existingUser == null)
        {
            return NotFound();
        }

        await _repository.Delete(existingUser);
        return NoContent();
    }
}
```

### Global error handling

For **.NET 8**:

1. Add these lines to your Program.cs:

   ```csharp
   ...
   builder.Services.AddExceptionHandler<GlobalExceptionHandler>();
   builder.Services.AddProblemDetails();

   ...
   app.UseExceptionHandler();
   ```

2. Create a ViewModel:

   ```csharp
   public class ErrorResponseViewModel
   {
       public string Message { get; set; }

       public int StatusCode { get; set; }
   }
   ```

3. Add and customize the global exception handler:

   ```csharp
   public class GlobalExceptionHandler : IExceptionHandler
   {
       private readonly ILogger _logger;
       public GlobalExceptionHandler(ILogger<GlobalExceptionHandler> logger)
       {
           _logger = logger;
       }

       public async ValueTask<bool> TryHandleAsync(
           HttpContext httpContext, Exception exception, CancellationToken cancellationToken)
       {
           _logger.LogError($"Exception [{ exception.Message }]. Inner exception [{ exception.InnerException?.Message}].  Stack trace: [{ exception.StackTrace }]");

           var errorResponse = new ErrorResponseViewModel();

           switch (exception)
           {
               case BadHttpRequestException:
                   errorResponse.StatusCode = (int)HttpStatusCode.BadRequest;
                   errorResponse.Message = exception.GetType().Name;
                   break;
               default:
                   errorResponse.StatusCode = (int)HttpStatusCode.InternalServerError;
                   errorResponse.Message = "Internal Server Error";
                   break;
           }

           httpContext.Response.StatusCode = errorResponse.StatusCode;
           await httpContext
               .Response
               .WriteAsJsonAsync(errorResponse, cancellationToken);

           // You may return false and set another ExceptionHandler to handle specific exceptions
           return true;
       }
   }
   ```

More info:
* [Global error handling .NET 8](https://code-maze.com/dotnet-use-iexceptionhandler-to-handle-exceptions/)
* [Global error handling tutorial](https://code-maze.com/global-error-handling-aspnetcore/)
* [Official guide](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/error-handling?view=aspnetcore-8.0)

### EF Core Hotspots

* Startup.cs -> Configure Services

  ```csharp
  services.AddDbContext<MYContext>(
      options => options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"),
      serverOptions => serverOptions.MigrationsAssembly("ASSEMBLYNAME")));
  ```

* appsettings.json

  ```json
  {
    "ConnectionStrings": {
      "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=DBNAME;Trusted_Connection=True;MultipleActiveResultSets=true"
    },
    ...
  }
  ```

* DbContext:

  ```csharp
  public class MYContext : DbContext
  {
    public MYContext() { }

    public MYContext(DbContextOptions<MYContext> options)
            : base(options) { }

    public DbSet<...> ....
  }
  ```

* Testing InMemoryDatabase provider:

  ```csharp
  new DbContextOptionsBuilder<MYContext>()
    .UseInMemoryDatabase(databaseName: NAME)
    .Options
  ```

### EF Core Commands

* Add migration:

```
dotnet ef migrations add NAME
```

* Remove last migration:

```
dotnet ef migrations remove
```

* Update database:

```
dotnet ef database update
```

* Drop (force):

```
dotnet ef database drop -f
```

* Start clean

```
dotnet ef database update 0
```

### AutoMapper DI

[AutoMapper Extensions for Microsoft DI](https://github.com/AutoMapper/AutoMapper.Extensions.Microsoft.DependencyInjection).

```csharp
// Add this line to your startup.cs
services.AddAutoMapper(AppDomain.CurrentDomain.GetAssemblies());
// Or this one...
services.AddAutoMapper(typeof(Program))
```

### Mediatr

* Install:
  ```
  dotnet add package MediatR
  ```

* Injection:
  ```csharp
  services.AddMediatR(cfg => {
      cfg.RegisterServicesFromAssembly(typeof(Program).Assembly);
  });
  ```

* Request / Response (single handler):
  ```csharp
  // Request with return
  public class Command : IRequest<string> // IRequest<ReturnType>
  {
    // Add command params here
  }
  // Response (note generic params)
  public class CommandHandler : IRequestHandler<Command, string> // IRequestHandler<Request, ReturnType>
  {
      public async Task<string> Handle(Command request, CancellationToken cancellationToken)
      {
          // You may need to read your command params
          // ...
          // ...
          return Task.FromResult("Result");
      }
  }
  // Send message from any other place
  var command = new Command();
  var response = await mediator.Send(Command);
  ```

  If you do not require a response:

  ```csharp
  public class OneWay : IRequest { }
  public class OneWayHandler : IRequestHandler<OneWay>
  {
      public async Task Handle(OneWay request, CancellationToken cancellationToken)
      {
          // Do work
          // ...
          return Task.CompletedTask;
      }
  }
  ```

* Notifications (multiple handlers):

  ```csharp
  public class MyEvent : INotification { }

  public class MyEventStore : INotificationHandler<MyEvent>
  {
      public async Task Handle(MyEvent notification, CancellationToken cancellationToken)
      {
          // Save in your DB
          // ...
          return Task.CompletedTask;
      }
  }

  public class MyEventNotify : INotificationHandler<MyEvent>
  {
      public async Task Handle(MyEvent notification, CancellationToken cancellationToken)
      {
          // Do work
          // ...
          return Task.CompletedTask;
      }
  }
  ```

* More info:
  * [Wiki](https://github.com/jbogard/MediatR/wiki)
  * [Official repo](https://github.com/jbogard/MediatR)

### Docker development (with Visual Studio)

1. Install [Docker Desktop](https://docs.docker.com/desktop/install/windows-install/) and configure it for **Linux Containers**.
2. Run these commands (you may need to enable CPU virtualization on your BIOS):

   ```sh
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

   # If update fails, try to install: wsl.exe --install
   wsl.exe --update

   wsl --set-default-version 2
   ```

3. Restart. Accept SSL certificates and Re-restart your machine.
4. You're ready to go!

* Problems? [Check this guide](https://learn.microsoft.com/en-us/windows/wsl/install-manual)

### Performance

* A kind of - Practical intro to [Async APIs](https://www.carlrippon.com/scalable-and-performant-asp-net-core-web-apis-asynchronous-operations/)
  * Async discussions:
    * [Sync vs Async](https://caleblloyd.com/software/net-core-mvc-thread-pool-vs-async/)
    * [Async and IIS](https://blogs.msdn.microsoft.com/rickandy/2011/07/19/should-my-database-calls-be-asynchronous-part-ii/)
    * [My take on Async - experiment](http://www.tugberkugurlu.com/archive/my-take-on-task-base-asynchronous-programming-in-c-sharp-5-0-and-asp-net-mvc-web-applications)

* Use DbContextPooling to improve the performance: [.Net Core 2.1](https://neelbhatt.com/2018/02/27/use-dbcontextpooling-to-improve-the-performance-net-core-2-1-feature/)


## Vortex

Write elegant and testeable solutions on C# using a Monadic Framework. ASP Core joins the functional side!

* [Vortex Repository](https://github.com/equilaterus/Vortex)
* [Sample Apps](https://github.com/equilaterus/Vortex.Samples)
* [Official website](https://equilaterus.github.io/Vortex/)
