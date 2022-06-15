# Background Workers
Background workers are simple independent threads in the application running in the background.

## Create a Background Worker
### `AsyncPeriodicBackgroundWorkerBase`

AsyncPeriodicBackgroundWorkerBase class simplifies to create periodic workers, so we will use it for the example below:

*Assume that we want to make a user passive, if the user has not logged in to the application in last 30 days.*

```
public class PassiveUserCheckerWorker : AsyncPeriodicBackgroundWorkerBase
{
    public PassiveUserCheckerWorker(
            AbpAsyncTimer timer,
            IServiceScopeFactory serviceScopeFactory
        ) : base(
            timer, 
            serviceScopeFactory)
    {
        Timer.Period = 600000; //10 minutes
    }

    protected async override Task DoWorkAsync(
        PeriodicBackgroundWorkerContext workerContext)
    {
        Logger.LogInformation("Starting: Setting status of inactive users...");

        //Resolve dependencies
        var userRepository = workerContext
            .ServiceProvider
            .GetRequiredService<IUserRepository>();

        //Do the work
        await userRepository.UpdateInactiveUserStatusesAsync();

        Logger.LogInformation("Completed: Setting status of inactive users...");
    }
}
```

- `AsyncPeriodicBackgroundWorkerBase` uses the `AbpTimer` (a thread-safe timer) object to determine **the period**. We can set its `Period` property in the constructor.
- It required to implement the `DoWorkAsync` method to **execute** the periodic work.
- It is a good practice to **resolve dependencies** from the `PeriodicBackgroundWorkerContext` instead of constructor injection. Because `AsyncPeriodicBackgroundWorkerBase` uses a `IServiceScope` that is **disposed** when your work finishes.
- `AsyncPeriodicBackgroundWorkerBase` catches and logs exceptions thrown by the **DoWorkAsync** method.

## Register Background Worker

After creating a background worker class, you should add it to the `IBackgroundWorkerManager`. The most common place is the `OnApplicationInitialization` method of your module class:

```
[DependsOn(typeof(AbpBackgroundWorkersModule))]
public class MyModule : AbpModule
{
    public override Task OnApplicationInitializationAsync(
        ApplicationInitializationContext context)
    {
        context.AddBackgroundWorkerAsync<PassiveUserCheckerWorker>();
    }
}
```

`context.AddBackgroundWorkerAsync(...)` is a shortcut extension method for the expression below:

```
await context.ServiceProvider
    .GetRequiredService<IBackgroundWorkerManager>()
    .AddAsync(
        context
            .ServiceProvider
            .GetRequiredService<PassiveUserCheckerWorker>()
    );
```

So, it resolves the given background worker and adds to the `IBackgroundWorkerManager`.

While we generally add workers in `OnApplicationInitialization`, there are no restrictions on that. You can inject `IBackgroundWorkerManager` anywhere and add workers at runtime. Background worker manager will stop and release all the registered workers when your application is being shut down.

## Making Your Application Always Run
Background workers only work if your application is running. If you host the background job execution in your web application (this is the default behavior), you should ensure that your web application is configured to always be running. Otherwise, background jobs only work while your application is in use.

> References
- [Background Worker](https://docs.abp.io/en/abp/latest/Background-Workers#introduction)