

# Areas of Concurrency


[http://www.drdobbs.com/parallel/the-pillars-of-concurrency/200001985](http://www.drdobbs.com/parallel/the-pillars-of-concurrency/200001985)

[https://d33ypg4xwx0n86.cloudfront.net/direct?url=http%3A%2F%2Ftwimgs.com%2Fddj%2Fimages%2Farticle%2F2010%2F1003%2Fpillar_ec_hs_table1.gif&resize=w640](https://d33ypg4xwx0n86.cloudfront.net/direct?url=http%3A%2F%2Ftwimgs.com%2Fddj%2Fimages%2Farticle%2F2010%2F1003%2Fpillar_ec_hs_table1.gif&resize=w640)

# Asynchronous

- it means that we continue execution after something finished
- this can happen in many ways (events, callbacks, promises, async/await)
- the opposite of async is blocking code, where we blocka and wait for something to finish
- I/O async: when working with I/O opperations
- event async: manually triggered like an event


## Responsivenes and isolation

- highest level of concurrency
- stay responsive by running things independently and asynchronously
- communicate via messages
- message-pumps: GUI, device, etc
- isolation is often key
- can be short lived or long running
- controls the flow of data in the application
- tech: Task.Run(), Rx streams, actor frameworks


## Throughput and scalability

- fine grained concurrency
- use more cores to get the answer faster
- can use data or task parallelism
- data parallelism: split data up and run in parallel on the groups
- static task parallelism: run a fixed number of computations in parallel
- dynamic task parallelism: run a dynamic number of computations in parallel
- usually localized to one method
- tech: PLINQ, Parallel.*, task.ContinueWith()


## Consistency

- low level concurrency
- avoid race conditions when accesing shared data
- prefer isolation of data where possible
- share data vie immutable structures
- if mutation is needed then limit access and synchronize (locks)
- synchronization via locks ideal is an implementation detail and done in low level code
- tech: locks, ConcurrentCollections, BlockingCollection, AutoResetEventSlim, ManualResetEventSlim, Interlocked.*
