
# Optional:

## async + OOP gotchas 

[https://blog.stephencleary.com/2013/01/async-oop-0-introduction.html](https://blog.stephencleary.com/2013/01/async-oop-0-introduction.html)

- constructor (initialize)
- properties (should be short)
- state (think of what the state is in-between)
- events (async events need plumbing)
- disposal (async disposal is needed when want to know that all work is done - Completion)


## Low level concurrency


[http://www.albahari.com/threading/part2.aspx](http://www.albahari.com/threading/part2.aspx)

- thread-safety
- immutable data
- data isolation
- locking
- concurrent data structures


## Streams


[http://www.introtorx.com/](http://www.introtorx.com/)

``` C#
Task<IEnumerable<int>>
IEnumerable<Task<int>>
IObserable<int>

IAsyncEnumerable<int>
```


## High level concurrency


[http://www.drdobbs.com/parallel/use-threads-correctly-isolation-asynch/215900465](http://www.drdobbs.com/parallel/use-threads-correctly-isolation-asynch/215900465)

[http://www.drdobbs.com/parallel/prefer-using-active-objects-instead-of-n/225700095](http://www.drdobbs.com/parallel/prefer-using-active-objects-instead-of-n/225700095)

- actors
- channels
- design
