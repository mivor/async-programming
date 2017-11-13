

# c# Async/await Introduction 


[https://blog.stephencleary.com/2012/02/async-and-await.html](https://blog.stephencleary.com/2012/02/async-and-await.html)

[https://msdn.microsoft.com/en-us/magazine/jj991977.aspx](https://msdn.microsoft.com/en-us/magazine/jj991977.aspx)

[https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)

[https://blogs.msdn.microsoft.com/pfxteam/2012/04/12/asyncawait-faq/](https://blogs.msdn.microsoft.com/pfxteam/2012/04/12/asyncawait-faq/)

[https://gist.github.com/jonlabelle/841146854b23b305b50fa5542f84b20c](https://gist.github.com/jonlabelle/841146854b23b305b50fa5542f84b20c)

[https://blogs.msdn.microsoft.com/pfxteam/](https://blogs.msdn.microsoft.com/pfxteam/)

## Keywords

- `await` 
    The `await` keyword is where things can get asynchronous. Await is like a unary operator: it takes a single argument, an awaitable (an “awaitable” is an asynchronous operation). Await examines that awaitable to see if it has already completed; if the awaitable has already completed, then the method just continues running (synchronously, just like a regular method).

    If `await` sees that the awaitable has not completed, then it acts asynchronously. It tells the awaitable to run the remainder of the method when it completes, and then returns from the async method.

- `async` enables the use of the `await` keyword


## Awaitables

- these are the types that can be awaited
- it's based on convention
- Task/Task<T> are awaitables
- there are others too
- 99% you don't have to think about this


## Contexts


[http://blog.stephencleary.com/2014/04/announcement-msdn-async-mvvm-articles.html](http://blog.stephencleary.com/2014/04/announcement-msdn-async-mvvm-articles.html)

[https://blog.stephencleary.com/2012/07/dont-block-on-async-code.html](https://blog.stephencleary.com/2012/07/dont-block-on-async-code.html)

- where to continue after an async part finishes?

Simple answer:

* If you’re on a UI thread, then it’s a UI context.
* If you’re responding to an ASP.NET request, then it’s an ASP.NET request context.
* Otherwise, it’s usually a thread pool context. (console app, background thread)

Complex answer:

* If SynchronizationContext.Current is not null, then it’s the current SynchronizationContext. (UI and ASP.NET request contexts are SynchronizationContext contexts).
* Otherwise, it’s the current TaskScheduler (TaskScheduler.Default is the thread pool context).

- makes it easy to "come back" to the UI thread an update values
- very good in application logic (ViewModels/Controller)
- not wanted in library logic (services, etc)

- to disable: 
``` C#
var task = DoSomethingAsync();
await task.ConfigureAwait(false);
// code no longer on UI thread
```



```C#
// DEADLOCKING CODE on UI or ASP.NET
// My "library" method.
public static async Task<JObject> GetJsonAsync(Uri uri)
{
  using (var client = new HttpClient())
  {
    var jsonString = await client.GetStringAsync(uri);
    return JObject.Parse(jsonString);
  }
}

// My UI VM code
public void ExecuteDownload(...)
{
  var jsonTask = GetJsonAsync(...);
  textBox1.Text = jsonTask.Result; // <- DEADLOCK
}
```


## API

### Task


[https://blog.stephencleary.com/2014/04/a-tour-of-task-part-0-overview.html](https://blog.stephencleary.com/2014/04/a-tour-of-task-part-0-overview.html)

### Basics

- will finish in 3 states (RanToCompletion, Canceled, Faulted)
- IsCompleted = finished running
- get returned value: await task or task.Result


#### A promise


[https://blog.stephencleary.com/2013/11/there-is-no-thread.html](https://blog.stephencleary.com/2013/11/there-is-no-thread.html)

- represents the result of an async API (I/O)
- used mainly with async/await
- there is no thread running it!
- we are only interested in: completion (succesful, caneled, faulter)
- DO NOT block on these

```C#
var joe = await dbContext.Users.FirstAsync(x => x.Name == "joe");
```


#### Event

- least used form
- represents a notification that can fire only once
- mostly done trough TaskCompletionSource
- used mainly as lifecycle notifications (Initalization, Completion, etc)
- sometimes not all 3 states are used, depends on implementation
- may block on these, depends on implementation!

```C#
public interface IComplete
{
    Task Completion {get;}
    void Seal();
}
// ...
queue.Seal();
// ...
await queue.Completion;
```

```C#
public async Task<NumericContentValue> CalibrateAsync()
{
    var tcs = new TaskCompletionSource<NumericContentValue>();
    ValueChangedEventHandler handler = (sender, args) => tcs.TrySetResult((NumericContentValue)args.NewValue);

    try
    {
        Component.CalibrationEnd += handler;
        Component.Calibrate();
        return await tcs.Task;
    }
    finally
    {
        Component.CalibrationEnd -= handler;
    }
}
```


#### An operation/delegate (code running somewhere)


[https://msdn.microsoft.com/en-us/library/ff963551.aspx](https://msdn.microsoft.com/en-us/library/ff963551.aspx)

[http://blog.stephencleary.com/2013/08/startnew-is-dangerous.html](http://blog.stephencleary.com/2013/08/startnew-is-dangerous.html)

- used for computation:
    * background work (Task.Run)
    * parallel (Parallel.*, PLINQ)
    * dynamic task parallelism (TaskFactory.StartNew)
- represents the state of that operation (Created, WaitingForActivation, WaitingToRun, etc)
- can have parent/child relationships
- supports chaining with: task.ContinueWith(...);
- it is acceptable to block on these

```c#
Result = await Task.Run(() => ComputeResult());
```


### Task.Run


[http://blog.stephencleary.com/2013/10/taskrun-etiquette-and-proper-usage.html](http://blog.stephencleary.com/2013/10/taskrun-etiquette-and-proper-usage.html)

[http://blog.stephencleary.com/2013/08/startnew-is-dangerous.html](http://blog.stephencleary.com/2013/08/startnew-is-dangerous.html)

- run code on a background thread (thread pool)
- can return a value
- understands async delegates: Task.Run(DoSomethingAsync)

#### DO:
- main purpose is to offload some work to a background thread to keep the current thread responsive
- mostly used to keep the UI responsive while doing something CPU intensive
- prefer this over other APIs like new Task().Start() or Task.Factory.StartNew()
- use it in application code if needed to wrap a blocking call

#### DO NOT:
- not for I/O operations, use native async methods wherever possible (EF, Steam, File, HttpClient, etc)
- do not use it in library/service code to "make something async"
- do not create sync versions of async methods
- document async and CPU intensive methods

``` C#
// BAD CODE
public void LoadDataAsync()
{
    return Task.Run(() => LoadData());
}
```

### Other Task.* methods

- Task.WhenAll
- Task.WhenAny
- Task.Delay
- Task.Yield
- Task.FromResult
- Task.CompletedTask
- Task.FromException
- Task.Cancelled



### TaskCompletionSource

- use to manually create and control the lifetime of a Task
- these are not bound to the execution of code
- useful when creating async data structures or wrapping non-Task based APIs (ex: EAP)
- has methods for: TrySet/Set Result/Exception/Cancel


### Exceptions

- await throws if there are exceptions
- it will thow the first exception, if there are more
- cancellations are also exceptions (OperationCancelled and TaskCancelled)
- task.Wait()/task.Result wraps all exceptions into an AggregatedException
- task.GetAwaiter().GetResult() does not wrap exceptions
- task.Exception does not throw. It's null if not faulted otherwise it's the exception.
- async void methods exceptions are re-thrown if there is a SynchronizationContext (UI)
- TaskScheduler.UnobservedTaskException [https://msdn.microsoft.com/en-us/library/system.threading.tasks.taskscheduler.unobservedtaskexception.aspx](https://msdn.microsoft.com/en-us/library/system.threading.tasks.taskscheduler.unobservedtaskexception.aspx)


### Cancellation

- CancellationTokenSource and CancellationToken
- caller creates Source and gives Token to method
- method decides when to check the Token: token.IsCanellationRequested or token.ThowIfCanellationRequested
- caller cancels with: source.Cancel()
- source has support for timeout and linking multiple cancellations
- CancellationTokenSource should be disposed


### Progress

- IProgress<T> and Progress<T>
- automatically delegates updates back to UI
- called method does reporting from any thread it's running on
- can be subclassed for advanced usages (ex: ProgressDialogs)


### Older Models

#### Async Programing Model (APM)
- Begin/End method paris + IAsyncResult
- use Task.Factory.FromAsync

#### Event-Based Async Pattern (EAP)
- DownloadDataAsync + DownloadDataCompleted
- use TaskCompletionSource to wrap the completion event into a Task

#### BackgroundWorker
- limited control when to update the UI DoWork + RunWorkerCompleted
- easy to refactor to 1:1 Task based version with Task.Run



#### Usages

task.Wait	        await task	        Wait/await for a task to complete
task.Result	        await task	        Get the result of a completed task
Task.WaitAny	    await Task.WhenAny	Wait/await for one of a collection of tasks to complete
Task.WaitAll	    await Task.WhenAll	Wait/await for all of a collection of tasks to complete
Thread.Sleep	    await Task.Delay	Wait/await for a period of time
Task constructor	Task.Run	        Create a code-based task (operation)
