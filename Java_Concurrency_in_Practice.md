# Chapter 7. Cancellation and Shutdown


### 7.1 Task cancellation

- There are a number of reasons why you might want to cancel an activity: User-requested cancellation, Time-limited activities, Application events (one task finds a solution), Errors (the disk is full), Shutdown
- A task that wants to be cancellable must have a __cancellation policy__ that specifies the “how”, “when”, and “what” of cancellation—how other code can request cancellation, when the task checks whether cancellation has been requested, and what actions the task takes in response to a cancellation request.
- Interruption is usually the most sensible way to implement cancellation.
- A good way to think about interruption is that it does not actually interrupt a running thread; it just requests that the thread interrupt itself at the next convenient opportunity.
- The most sensible __interruption policy__ is some form of thread-level or service-level cancellation: exit as quickly as practical, cleaning up if necessary, and possibly notifying some owning entity that the thread is exiting.
- there are two practical strategies for handling `InterruptedException`: Propagate the exception (possibly after some task-specific cleanup) OR Restore the interruption status so that code higher up on the call stack can deal with it.
- It is important to distinguish between how tasks and threads should react to interruption.
- When `Future.get` throws `InterruptedException` or `TimeoutException` and you know that the result is no longer needed by the program, cancel the task with `Future.cancel`.

> :point_right: ... is an InterruptedException a sensible outcome when calling your method? [stack](https://stackoverflow.com/a/3976377)

> :point_right: __Why invoke the method Thread.currentThread.interrupt() in the catch block?__ - By convention, any method that exits by throwing an InterruptedException clears interrupt status when it does so. However, it's always possible that interrupt status will immediately be set again, by another thread invoking interrupt.
[stack](https://stackoverflow.com/q/4906799/5253591), [oracle](https://docs.oracle.com/javase/tutorial/essential/concurrency/interrupt.html)
