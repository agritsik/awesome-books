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
- Interruption is usually the most sensible way to implement cancellation.
- A good way to think about interruption is that it does not actually interrupt a running thread; it just requests that the thread interrupt itself at the next convenient opportunity.
- The most sensible __interruption policy__ is some form of thread-level or service-level cancellation: exit as quickly as practical, cleaning up if necessary, and possibly notifying some owning entity that the thread is exiting.
- there are two practical strategies for handling `InterruptedException`: Propagate the exception (possibly after some task-specific cleanup) OR Restore the interruption status so that code higher up on the call stack can deal with it.
- It is important to distinguish between how tasks and threads should react to interruption.
- When `Future.get` throws `InterruptedException` or `TimeoutException` and you know that the result is no longer needed by the program, cancel the task with `Future.cancel`.

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
