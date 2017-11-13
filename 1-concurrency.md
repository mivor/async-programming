# Concurrency


[https://www.youtube.com/watch?v=cN_DpYBzKso](https://www.youtube.com/watch?v=cN_DpYBzKso)

[http://joeduffyblog.com/2016/11/30/15-years-of-concurrency/](http://joeduffyblog.com/2016/11/30/15-years-of-concurrency/)

[https://qconlondon.com/london-2017/system/files/keynotes-slides/2017.qconlondon.duffy-keynote.pdf](https://qconlondon.com/london-2017/system/files/keynotes-slides/2017.qconlondon.duffy-keynote.pdf)

- the world is object oriented?
- nope. It's process oriented! 
- Things happen independently at the same time.


## Concurrency
Design

- dealing with a lot of things at once
- How to express the problem/program

- could enable parallelism, but it's not required
- good structure is the goal
- break a program up into independent pieces that communicate
- communication is key


## Parallelism
Execution

- doing a lot of things at once
- at the scale of threads and processes
- How the program is executed

- requires a multi-core CPU


## Distributed
Execution

- different scale than parallelism but same idea
- at the scale of containers/VMs/machines
- latency is higher and additional challanges

