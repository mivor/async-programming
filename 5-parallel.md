
# Parallel programming


[http://www.albahari.com/threading/part5.aspx#_Parallel_Programming](http://www.albahari.com/threading/part5.aspx#_Parallel_Programming)

## PLINQ (Data Paralellism)

- use .AsParallel() == magic!
- made for computations not I/O
- this is not asynchronous, the query is enumerated as expected
- by default item ordering will be lost
- can use .AsOrdered() + .AsUnordered() 
- .WithDegreeOfParallelism(4) - force nr of threads
- .WithCancellation()
- buffering is used to help performance, can be configured
- ParallelEnumerable.* for better perf

``` C#
// letter fequency accumulator
int[] result =
text.AsParallel().Aggregate (
    // Create a new local accumulator
    () => new int[26], 
    // Aggregate into the local accumulator
    (localFrequencies, c) => 
    {
        int index = char.ToUpper (c) - 'A';
        if (index >= 0 && index <= 26) localFrequencies [index]++;
        return localFrequencies;
    },
    // Aggregate local->main accumulator
    (mainFreq, localFreq) => mainFreq
        .Zip (localFreq, (f1, f2) => f1 + f2)
        .ToArray(),
    // Perform any final transformation on the end result.
    finalResult => finalResult
);                             
``` 


## Parallel.* (Static Task Parallelism)

### Invoke
- execute any number of delegates in parallel
- can handle up to millions of delegates efficiently
- result collection is up to the application, must handle thread-safety

### For/Foreach
- similar to regular version, executes a delegate as the body
- best suited when the delegate does some computation
- must handle thread safety
- use ParallelLoopResult parameter for controlling the loop and optimized data management



## Task (Dynamic Task Parallelism)


[https://msdn.microsoft.com/en-us/library/ff963551.aspx](https://msdn.microsoft.com/en-us/library/ff963551.aspx)

[http://www.albahari.com/threading/part5.aspx#_Task_Parallelism](http://www.albahari.com/threading/part5.aspx#_Task_Parallelism)

[http://www.albahari.com/threading/Continuations.png](http://www.albahari.com/threading/Continuations.png)

- best suited when dynamic creation of work items is needed
- examples: parallel tree traversal, parallel quicksort
- can create parent-child tasks
- different task continuation options
