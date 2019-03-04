# Chapter 6. Task Execution
- Most concurrent applications are organized around the execution of tasks: abstract, discrete units of work... Ideally, tasks are independent activities: work that doesn’t depend on the state, result, or side effects of other tasks.
- Disadvantages of unbounded thread creation: 
    - thread creation and teardown are not free
    - active threads consume system resources (especially memory)
    - there is a limit on how many threads can be created.

### ExecutorService
- The primary abstraction for task execution in the Java class libraries is not `Thread`, but `Executor`. It provides a standard means of decoupling task submission from task execution, describing tasks with Runnable... `Executor` is based on the __producer-consumer pattern__
- The value of decoupling submission from execution is that it lets you easily specify, and subsequently change without great difficulty, the __execution policy__ for a given class of tasks.
    - In what thread will tasks be executed?
    - In what order should tasks be executed (FIFO, LIFO, priority order)?
    - How many tasks may execute concurrently?
    - How many tasks may be queued pending execution?
    - Rejection policy etc
- The __lifecycle__ of a task executed by an `Executor` has four phases: created, submitted, started, and completed. `Future` represents the lifecycle of a task and provides methods to test whether the task has completed or been cancelled, retrieve its result, and cancel the task... Implicit in the specification of `Future` is that task lifecycle can only move forwards, not backwards—just like the `ExecutorService` lifecycle. Once a task is completed, it stays in that state forever.
- The real performance payoff of dividing a program’s workload into tasks comes when there are a large number of independent, homogeneous tasks that can be processed concurrently.

### Exploitable Parallelism
- `CompletionService` combines the functionality of an `Executor` and a `BlockingQueue`. You can submit `Callable` tasks to it for execution and use the queue-like methods take and `poll` to retrieve completed results, packaged as `Futures`, as they become available.
- The timed version of `invokeAll` will return when all the tasks have completed, the calling thread is interrupted, or the timeout expires. Any tasks that are not complete when the timeout expires are cancelled. On return from `invokeAll`, each task will have either completed normally or been cancelled;


# Chapter 7. Cancellation and Shutdown

### 7.1 Task cancellation

- There are a number of reasons why you might want to cancel an activity: User-requested cancellation, Time-limited activities, Application events (one task finds a solution), Errors (the disk is full), Shutdown
- A task that wants to be cancellable must have a __cancellation policy__ that specifies the “how”, “when”, and “what” of cancellation—how other code can request cancellation, when the task checks whether cancellation has been requested, and what actions the task takes in response to a cancellation request.
- Each thread has a boolean __interrupted status__; interrupting a thread sets its interrupted status to true... Calling interrupt does not necessarily stop the target thread from doing what it is doing; it merely delivers the message that interruption has been requested... _Interruption_ is usually the most sensible way to implement cancellation.
- A good way to think about _interruption_ is that it does not actually interrupt a running thread; it just requests that the thread interrupt itself at the next convenient opportunity.
- The most sensible __interruption policy__ is some form of thread-level or service-level cancellation: exit as quickly as practical, cleaning up if necessary, and possibly notifying some owning entity that the thread is exiting.
- there are two practical strategies for handling `InterruptedException`: Propagate the exception (possibly after some task-specific cleanup) OR Restore the interruption status so that code higher up on the call stack can deal with it.
- It is important to distinguish between how tasks and threads should react to interruption... Tasks do not execute in threads they own; they borrow threads owned by a service such as a thread pool.
    - most blocking library methods simply throw `InterruptedException` in response to an interrupt. They will never execute in a thread they own, so they implement the most reasonable cancellation policy for task or library code: get out of the way as quickly as possible and communicate the interruption back to the caller so that code higher up on the call stack can take further action.
    -  If it is not simply going to propagate `InterruptedException` to its caller, it should restore the interruption status after catching InterruptedException: `Thread.currentThread().interrupt();`
- :warning: When `Future.get` throws `InterruptedException` or `TimeoutException` and you know that the result is no longer needed by the program, cancel the task with `Future.cancel`.

> :point_right: ... is an InterruptedException a sensible outcome when calling your method? [stack](https://stackoverflow.com/a/3976377)

> :point_right: __Why invoke the method Thread.currentThread.interrupt() in the catch block?__ - By convention, any method that exits by throwing an InterruptedException clears interrupt status when it does so. However, it's always possible that interrupt status will immediately be set again, by another thread invoking interrupt.
[stack](https://stackoverflow.com/q/4906799/5253591), [oracle](https://docs.oracle.com/javase/tutorial/essential/concurrency/interrupt.html)

> :point_right: __What does Future.cancel() do if not interrupting?__ - If it is not interrupting it will simply tell the future that is is cancelled. You can check that via isCancelled() but nothing happens if you don't check that manually. [stack](https://stackoverflow.com/a/21445268/5253591)

> :point_right: __Does a Future timeout kill the Thread execution__ - It does not. Why would it? Unless you tell it to.
... `future.cancel(true);` does not stop the running underlying task, it just sets the interrupted flag to true for the running thread. It is your code that is responsible to check this flag and throw InterruptedException if it is true. [stack](https://stackoverflow.com/questions/16231508/does-a-future-timeout-kill-the-thread-execution)


```java
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executorService = Executors.newFixedThreadPool(4);
        Future<Integer> future = executorService.submit(() -> {
            System.out.println("start...");
            Thread.sleep(2000);
            System.out.println("stop...");
            return 1;
        });

        try {
            future.get(1000, TimeUnit.MILLISECONDS);
        } catch (ExecutionException e) {
            e.printStackTrace();
        } catch (TimeoutException e) {
            e.printStackTrace();
        }finally {
//            future.cancel(false); // you will see 'stop...'
//            future.cancel(true); // you won't see 'stop...'
        }
        executorService.shutdown();
    }
```


# Chapter 8. Applying Thread Pools

- Thread pools work best when tasks are homogeneous and independent. Types of tasks that require _specific execution policies_ include:
    - Dependent tasks.
    - Tasks that exploit thread confinement. 
    - Responsetime-sensitive tasks
    - Tasks that use ThreadLocal.
- :warning: Whenever you submit to an Executor tasks that are not independent, be aware of the possibility of thread __starvation deadlock__

### 8.2 Sizing thread pools

- to size a thread pool properly, you need to understand your computing environment, your resource budget, and the nature of your tasks. How many processors does the deployment system have? How much memory? Do tasks perform mostly computation, I/O, or some combination? Do they require a scarce resource, such as a JDBC connection? 
- for computeintensive tasks - `Ncpu + 1` threads 
- for I/O or other blocking operations - estimate the ratio of waiting time (this estimate need not be precise and can be obtained through profiling or instrumentation)
- the optimal pool size for keeping the processors at the desired utilization is: `Nthreads=Ncpu * Ucpu * (1+W/C)`

### 8.3. Configuring ThreadPoolExecutor

- The _core pool size_, _maximum pool size_, and _keep-alive time_ govern thread creation and teardown. 
- `ThreadPoolExecutor` allows you to supply a `BlockingQueue` to hold tasks awaiting execution. __There are three basic approaches to task queueing:__
    - Unbounded `LinkedBlockingQueue`. Tasks will queue up if all worker threads are busy, but the queue could grow without bound if the tasks keep arriving faster than they can be executed. (default for `newFixedThreadPool` and `newSingleThreadExecutor`)
    - A more stable resource management strategy is to use a bounded queue, such as an `ArrayBlockingQueue` or a bounded `LinkedBlockingQueue` or `PriorityBlockingQueue`. Bounded queues help prevent resource exhaustion but introduce the question of what to do with new tasks when the queue is full. With a bounded work queue, the _queue size_ and _pool size_ must be tuned together.
    - A `SynchronousQueue` is not really a queue at all, but a mechanism for managing handoffs between threads.  Using a direct handoff is more efficient because the task can be handed right to the thread that will execute it, rather than first placing it on a queue and then having the worker thread fetch it from the queue.
- :warning: Bounding either the thread pool or the work queue is suitable only when tasks are independent. With tasks that depend on other tasks, bounded thread pools or queues can cause thread __starvation deadlock__
- When a bounded work queue fills up, the __saturation policy__ comes into play. Several implementations of `RejectedExecutionHandler` are provided, each implementing a different saturation policy: `AbortPolicy`, `CallerRunsPolicy`, `DiscardPolicy`, and `DiscardOldestPolicy`.
    - the default policy, __abort__, causes execute to throw the unchecked `RejectedExecutionException`
    - the __discard__ policy silently discards the newly submitted task if it cannot be queued for execution
    - the __discard-oldest__ policy discards the task that would otherwise be executed next and tries to resubmit the new task. 
    - the __caller-runs__ policy implements a form of throttling that neither discards tasks nor throws an exception, but instead tries to slow down the flow of new tasks by pushing some of the work back to the caller.  
- :bulb: There is no predefined saturation policy to make `execute` block when the work queue is full. However, the same effect can be accomplished by using a `Semaphore` to bound the task injection rate. In such an approach, use an unbounded queue (there’s no reason to bound both the queue size and the injection rate) and set the bound on the semaphore to be equal to the pool size _plus_ the number of queued tasks you want to allow, since the semaphore is bounding the number of tasks both currently executing and awaiting execution.

