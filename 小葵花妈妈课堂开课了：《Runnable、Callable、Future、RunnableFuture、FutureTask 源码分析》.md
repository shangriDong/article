先看一下官方介绍：

```
/**
 * Runnable接口 被任意class实现的实例，都是由thread去执行的。
 * 该类必须要实现一个无参的run方法。
 * 当runnable被激活时将要执行的代码。
 * 例如实现了Runnable接口的Thread类。
 * 激活简单来说就是thread已经开始执行并且没有被停止。
 * 此外，Runnable提供给class的是接口方法来激活，而不是Thread去继承。
 * 在没有继承Thread类的情形下可以通过实例化的线程去执行实现了Runnable的类。
 * 在大多数情况下，如果你计划重写run方法并且没有其他线程方法，那么runnable接口就应该被使用。
 * 这样做是重要的，因为类不能被继承，除非开发人员打算修改或是增强类的功能。
 */
```
下面是原文：

```
/**
 * The <code>Runnable</code> interface should be implemented by any
 * class whose instances are intended to be executed by a thread. The
 * class must define a method of no arguments called <code>run</code>.
 * <p>
 * This interface is designed to provide a common protocol for objects that
 * wish to execute code while they are active. For example,
 * <code>Runnable</code> is implemented by class <code>Thread</code>.
 * Being active simply means that a thread has been started and has not
 * yet been stopped.
 * <p>
 * In addition, <code>Runnable</code> provides the means for a class to be
 * active while not subclassing <code>Thread</code>. A class that implements
 * <code>Runnable</code> can run without subclassing <code>Thread</code>
 * by instantiating a <code>Thread</code> instance and passing itself in
 * as the target.  In most cases, the <code>Runnable</code> interface should
 * be used if you are only planning to override the <code>run()</code>
 * method and no other <code>Thread</code> methods.
 * This is important because classes should not be subclassed
 * unless the programmer intends on modifying or enhancing the fundamental
 * behavior of the class.
 *
 * @author  Arthur van Hoff
 * @see     java.lang.Thread
 * @see     java.util.concurrent.Callable
 * @since   JDK1.0
 */
```

```
/**
 * When an object implementing interface <code>Runnable</code> is used
 * to create a thread, starting the thread causes the object's
 * <code>run</code> method to be called in that separately executing
 * thread.
 * <p>
 * The general contract of the method <code>run</code> is that it may
 * take any action whatsoever.
 *
 * @see     java.lang.Thread#run()
 */
/**
 * 当一个对象实现了Runnable接口用来创建一个线程，启动线程会引起对象的run方法在制定的线程中执行。
 * run方法可以执行任何action.
 * run方法没有返回值。
 */
public abstract void run();
```

再看一下Callable的官方介绍：

```
/**
 * task会返回一个结果并且可能抛出异常。
 * 定义了一个单一的无参的方法叫做call。
 * Callable接口类似于Runnable, 都是设计成为由另一个线程执行。
 * Runnable无论如何都不能返回结果并且也不能抛出异常。
 * 
 * Executors类包含了实用的方法来转换从其他常见形式转换为Callable类。
 */
```

下面是原文：

```

/**
 * A task that returns a result and may throw an exception.
 * Implementors define a single method with no arguments called
 * {@code call}.
 *
 * <p>The {@code Callable} interface is similar to {@link
 * java.lang.Runnable}, in that both are designed for classes whose
 * instances are potentially executed by another thread.  A
 * {@code Runnable}, however, does not return a result and cannot
 * throw a checked exception.
 *
 * <p>The {@link Executors} class contains utility methods to
 * convert from other common forms to {@code Callable} classes.
 *
 * @see Executor
 * @since 1.5
 * @author Doug Lea
 * @param <V> the result type of method {@code call}
 */
@FunctionalInterface
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
     
     /**
      * call方法会返回结果，如果不能计算出结果的话会抛出异常。
      */
    V call() throws Exception;
}
```

通过官方解释可以看出：
共同点： 

 - Runnable和Callable都是用Thread去执行

区别：

 - Runnable没有返回值，不会抛出异常
 - Callable有返回值，会抛出异常

**Future**
官方介绍：

```
/**
 * Future代表着一个异步计算的结果。方法可以去检测计算是否完成，可以等待计算结果，并且可以取回计算结果。
 * 提供方法有：检测计算是否完成，如果如果没有计算完成会一直等待计算结果。
 * 当计算完成后，仅可以使用get方法或得结果，如果没有完成会等待知道完成计算。
 * 可以通过cancel进行取消计算。
 * 还提供了方法来检查任务正常结束，还是被取消了。
 * 一旦计算完成，任务就不可被取消。
 * 如果使用Future不是为了拿到一个可用的结果，你可以通过Future<?>定义类型，并且返回null作为task的结果。
 * 
 */
```
原文：

```
/**
 * A {@code Future} represents the result of an asynchronous
 * computation.  Methods are provided to check if the computation is
 * complete, to wait for its completion, and to retrieve the result of
 * the computation.  The result can only be retrieved using method
 * {@code get} when the computation has completed, blocking if
 * necessary until it is ready.  Cancellation is performed by the
 * {@code cancel} method.  Additional methods are provided to
 * determine if the task completed normally or was cancelled. Once a
 * computation has completed, the computation cannot be cancelled.
 * If you would like to use a {@code Future} for the sake
 * of cancellability but not provide a usable result, you can
 * declare types of the form {@code Future<?>} and
 * return {@code null} as a result of the underlying task.
 *
 * <p>
 * <b>Sample Usage</b> (Note that the following classes are all
 * made-up.)
 *
 * <pre> {@code
 * interface ArchiveSearcher { String search(String target); }
 * class App {
 *   ExecutorService executor = ...
 *   ArchiveSearcher searcher = ...
 *   void showSearch(final String target)
 *       throws InterruptedException {
 *     Future<String> future
 *       = executor.submit(new Callable<String>() {
 *         public String call() {
 *             return searcher.search(target);
 *         }});
 *     displayOtherThings(); // do other things while searching
 *     try {
 *       displayText(future.get()); // use future
 *     } catch (ExecutionException ex) { cleanup(); return; }
 *   }
 * }}</pre>
 *
 * The {@link FutureTask} class is an implementation of {@code Future} that
 * implements {@code Runnable}, and so may be executed by an {@code Executor}.
 * For example, the above construction with {@code submit} could be replaced by:
 * <pre> {@code
 * FutureTask<String> future =
 *   new FutureTask<>(new Callable<String>() {
 *     public String call() {
 *       return searcher.search(target);
 *   }});
 * executor.execute(future);}</pre>
 *
 * <p>Memory consistency effects: Actions taken by the asynchronous computation
 * <a href="package-summary.html#MemoryVisibility"> <i>happen-before</i></a>
 * actions following the corresponding {@code Future.get()} in another thread.
 *
 * @see FutureTask
 * @see Executor
 * @since 1.5
 * @author Doug Lea
 * @param <V> The result type returned by this Future's {@code get} method
 */
```

介绍接口定义：

```
public interface Future<V> {
	/**
	* Attempts to cancel execution of this task.  This attempt will
	* fail if the task has already completed, has already been cancelled,
	* or could not be cancelled for some other reason. If successful,
	* and this task has not started when {@code cancel} is called,
	* this task should never run.  If the task has already started,
	* then the {@code mayInterruptIfRunning} parameter determines
	* whether the thread executing this task should be interrupted in
	* an attempt to stop the task.
	*
	* <p>After this method returns, subsequent calls to {@link #isDone} will
	* always return {@code true}.  Subsequent calls to {@link #isCancelled}
	* will always return {@code true} if this method returned {@code true}.
	*
	* @param mayInterruptIfRunning {@code true} if the thread executing this
	* task should be interrupted; otherwise, in-progress tasks are allowed
	* to complete
	* @return {@code false} if the task could not be cancelled,
	* typically because it has already completed normally;
	* {@code true} otherwise
	*/
	/**
	* 该方法为取消在执行的task，
	* 如果计算已经完成或者已经取消再或者因为其他原因不能取消，则取消失败。
	* 如果成功，并且在调用cancel取消时，还没有开始，task就不会在执行了。
	* 如果task已经开始了，那么mayInterruptIfRunning这个参数就会决定，执行任务的线程是否尝试中断task.
	* 当该方法cancel返回结果后，后来在调用isDone将一直返回true。
	* 之后在调用isCancelled会一直返回true，如果cancel之前返回true。正在进行的task仍会继续计算。
	* 
	* 如果结果返回false 也就是取消失败，通常是因为他已经正常结束了。
	* 
	* mayInterruptIfRunning 参数如果为true, 则执行task的线程会中断；否则
	*/
	boolean cancel(boolean mayInterruptIfRunning);
	
	/**
	* Returns {@code true} if this task was cancelled before it completed
	* normally.
	*
	* @return {@code true} if this task was cancelled before it completed
	* 如果在任务正常结束之前调用了cancel 那么会返回true。
	*/
	boolean isCancelled();
	
	/**
	* Returns {@code true} if this task completed.
	*
	* Completion may be due to normal termination, an exception, or
	* cancellation -- in all of these cases, this method will return
	* {@code true}.
	* 
	* 如果过task计算完成则返回true。
	* 计算也许是正常结束，也许异常结束，或许被取消， 这些情况该方法都会返回true。
	* 
	* @return {@code true} if this task completed
	*/
	boolean isDone();
	
	/**
	* Waits if necessary for the computation to complete, and then
	* retrieves its result.
	* 
	* 必要时就会等待计算完成，并且返回计算结果。
	* 
	* @return the computed result
	* @throws CancellationException if the computation was cancelled
	* 如果计算被取消
	* @throws ExecutionException if the computation threw an
	* exception
	* 如果计算抛出异常
	* @throws InterruptedException if the current thread was interrupted
	* while waiting
	* 如果在等待时线程呗中断则会跑异常
	*/
	V get() throws InterruptedException, ExecutionException;
	
	/**
	* Waits if necessary for at most the given time for the computation
	* to complete, and then retrieves its result, if available.
	* 
	* 需要等待时，会最多等待给定的时长，并返回结果。
	* 
	* @param timeout the maximum time to wait
	* @param unit the time unit of the timeout argument
	* @return the computed result
	* @throws CancellationException if the computation was cancelled
	* @throws ExecutionException if the computation threw an
	* exception
	* @throws InterruptedException if the current thread was interrupted
	* while waiting
	* @throws TimeoutException if the wait timed out
	*/
	V get(long timeout, TimeUnit unit)
		  throws InterruptedException, ExecutionException, TimeoutException;
}
```

**RunnableFuture**

```
/**
 * A {@link Future} that is {@link Runnable}. Successful execution of
 * the {@code run} method causes completion of the {@code Future}
 * and allows access to its results.
 * @see FutureTask
 * @see Executor
 * @since 1.6
 * @author Doug Lea
 * @param <V> The result type returned by this Future's {@code get} method
 */
 //Futrue即为Runnable。因为继承Future所以可以执行run方法。
 //并且可以接受返回的结果。
public interface RunnableFuture<V> extends Runnable, Future<V> {
    /**
     * Sets this Future to the result of its computation
     * unless it has been cancelled.
     */
    void run();
}
```
**FutureTask**
FutureTask 实现了RunnableFutrue接口
官方介绍：

```
FutureTask提供可以取消的异步计算任务。
这个类实现了Future接口方法，铜鼓这些方法可以开始和取消计算，还可以查询计算是否完成和接受计算返回的结果。
结果只能在计算完成后接收。如果计算还没有完成get方法会阻塞。
如果计算完成，计算就不能再次重新开始或者取消（除非使用runAndReset再次调起）。

FutureTask可以用来包装Callable或者Runnable。
因为FutureTask实现了Runnable，FutrueTask可以提交给Executor来执行。

除了作为独立类之外，这个类还提供了protected方法，这些方法是对创建自定义task时是有帮助的。
/**
 * A cancellable asynchronous computation.  This class provides a base
 * implementation of {@link Future}, with methods to start and cancel
 * a computation, query to see if the computation is complete, and
 * retrieve the result of the computation.  The result can only be
 * retrieved when the computation has completed; the {@code get}
 * methods will block if the computation has not yet completed.  Once
 * the computation has completed, the computation cannot be restarted
 * or cancelled (unless the computation is invoked using
 * {@link #runAndReset}).
 *
 * <p>A {@code FutureTask} can be used to wrap a {@link Callable} or
 * {@link Runnable} object.  Because {@code FutureTask} implements
 * {@code Runnable}, a {@code FutureTask} can be submitted to an
 * {@link Executor} for execution.
 *
 * <p>In addition to serving as a standalone class, this class provides
 * {@code protected} functionality that may be useful when creating
 * customized task classes.
 *
 * @since 1.5
 * @author Doug Lea
 * @param <V> The result type returned by this FutureTask's {@code get} methods
 */
```
解析FutureTask

```
public class FutureTask<V> implements RunnableFuture<V> {
    /*
     * Revision notes: This differs from previous versions of this
     * class that relied on AbstractQueuedSynchronizer, mainly to
     * avoid surprising users about retaining interrupt status during
     * cancellation races. Sync control in the current design relies
     * on a "state" field updated via CAS to track completion, along
     * with a simple Treiber stack to hold waiting threads.
     *
     * Style note: As usual, we bypass overhead of using
     * AtomicXFieldUpdaters and instead directly use Unsafe intrinsics.
     */
	/**
	 * 版本标注：不同于之前的版本，当前类依靠AbstractQueuedSynchronizer，
	 * 主要是为了避免用户关于始终保持中断状态在取消的时候。在当前设计中同步控制
	 * 依赖于state字段，该字段通过CAS更新，并和Treiber栈去持有等待线程。
	 */
    /**
     * The run state of this task, initially NEW.  The run state
     * transitions to a terminal state only in methods set,
     * setException, and cancel.  During completion, state may take on
     * transient values of COMPLETING (while outcome is being set) or
     * INTERRUPTING (only while interrupting the runner to satisfy a
     * cancel(true)). Transitions from these intermediate to final
     * states use cheaper ordered/lazy writes because values are unique
     * and cannot be further modified.
     *
     * Possible state transitions:
     * NEW -> COMPLETING -> NORMAL
     * NEW -> COMPLETING -> EXCEPTIONAL
     * NEW -> CANCELLED
     * NEW -> INTERRUPTING -> INTERRUPTED
     */
    /**
     * 这个task的运行状态，初始化为NEW。
     * 仅能通过set,setException和cancel将运行状态转变为中断状态。
     * 在计算时候，state可以呈现出COMPLETING的值（虽然结果正在设定）或者INTERRUPTING的值
     * （仅当使用中断来取消的时候）
     * 使用廉价的命令和懒写入从中间状态过度到最终状态，因为值是唯一的并且不能进一步修改。
     * 可能存在的状态转化情况：
     * NEW -> COMPLETING -> NORMAL
     * NEW -> COMPLETING -> EXCEPTIONAL
     * NEW -> CANCELLED
     * NEW -> INTERRUPTING -> INTERRUPTED
     */
    private volatile int state;
    private static final int NEW          = 0;
    private static final int COMPLETING   = 1;
    private static final int NORMAL       = 2;
    private static final int EXCEPTIONAL  = 3;
    private static final int CANCELLED    = 4;
    private static final int INTERRUPTING = 5;
    private static final int INTERRUPTED  = 6;

    /** The underlying callable; nulled out after running */
    // 底层调用callable;清零后调用
    private Callable<V> callable;
    
    /** The result to return or exception to throw from get() */
    //返回额结果或者是get()抛出的异常 非volatile ，通过reads/writes状态来保护安全性
    private Object outcome; // non-volatile, protected by state reads/writes
    
    /** The thread running the callable; CASed during run() */
    private volatile Thread runner;
    /** Treiber stack of waiting threads */
    private volatile WaitNode waiters;

    /**
     * Returns result or throws exception for completed task.
     *
     * @param s completed state value
     */
    @SuppressWarnings("unchecked")
    private V report(int s) throws ExecutionException {
        Object x = outcome;
        if (s == NORMAL)
            return (V)x;
        if (s >= CANCELLED)
            throw new CancellationException();
        throw new ExecutionException((Throwable)x);
    }

    /**
     * Creates a {@code FutureTask} that will, upon running, execute the
     * given {@code Callable}.
     *
     * @param  callable the callable task
     * @throws NullPointerException if the callable is null
     */
    public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;       // ensure visibility of callable
    }

    /**
     * Creates a {@code FutureTask} that will, upon running, execute the
     * given {@code Runnable}, and arrange that {@code get} will return the
     * given result on successful completion.
     *
     * @param runnable the runnable task
     * @param result the result to return on successful completion. If
     * you don't need a particular result, consider using
     * constructions of the form:
     * {@code Future<?> f = new FutureTask<Void>(runnable, null)}
     * @throws NullPointerException if the runnable is null
     */
    public FutureTask(Runnable runnable, V result) {
        this.callable = Executors.callable(runnable, result);
        this.state = NEW;       // ensure visibility of callable
    }

    public boolean isCancelled() {
        return state >= CANCELLED;
    }

    public boolean isDone() {
        return state != NEW;
    }

    public boolean cancel(boolean mayInterruptIfRunning) {
        if (!(state == NEW &&
              U.compareAndSwapInt(this, STATE, NEW,
                  mayInterruptIfRunning ? INTERRUPTING : CANCELLED)))
            return false;
        try {    // in case call to interrupt throws exception
            if (mayInterruptIfRunning) {
                try {
                    Thread t = runner;
                    if (t != null)
                        t.interrupt();
                } finally { // final state
                    U.putOrderedInt(this, STATE, INTERRUPTED);
                }
            }
        } finally {
            finishCompletion();
        }
        return true;
    }

    /**
     * @throws CancellationException {@inheritDoc}
     */
    public V get() throws InterruptedException, ExecutionException {
        int s = state;
        if (s <= COMPLETING)
            s = awaitDone(false, 0L);
        return report(s);
    }

    /**
     * @throws CancellationException {@inheritDoc}
     */
    public V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException {
        if (unit == null)
            throw new NullPointerException();
        int s = state;
        if (s <= COMPLETING &&
            (s = awaitDone(true, unit.toNanos(timeout))) <= COMPLETING)
            throw new TimeoutException();
        return report(s);
    }

    /**
     * Protected method invoked when this task transitions to state
     * {@code isDone} (whether normally or via cancellation). The
     * default implementation does nothing.  Subclasses may override
     * this method to invoke completion callbacks or perform
     * bookkeeping. Note that you can query status inside the
     * implementation of this method to determine whether this task
     * has been cancelled.
     */
    protected void done() { }

    /**
     * Sets the result of this future to the given value unless
     * this future has already been set or has been cancelled.
     *
     * <p>This method is invoked internally by the {@link #run} method
     * upon successful completion of the computation.
     *
     * @param v the value
     */
    protected void set(V v) {
        if (U.compareAndSwapInt(this, STATE, NEW, COMPLETING)) {
            outcome = v;
            U.putOrderedInt(this, STATE, NORMAL); // final state
            finishCompletion();
        }
    }

    /**
     * Causes this future to report an {@link ExecutionException}
     * with the given throwable as its cause, unless this future has
     * already been set or has been cancelled.
     *
     * <p>This method is invoked internally by the {@link #run} method
     * upon failure of the computation.
     *
     * @param t the cause of failure
     */
    protected void setException(Throwable t) {
        if (U.compareAndSwapInt(this, STATE, NEW, COMPLETING)) {
            outcome = t;
            U.putOrderedInt(this, STATE, EXCEPTIONAL); // final state
            finishCompletion();
        }
    }

    public void run() {
        if (state != NEW ||
            !U.compareAndSwapObject(this, RUNNER, null, Thread.currentThread()))
            return;
        try {
            Callable<V> c = callable;
            if (c != null && state == NEW) {
                V result;
                boolean ran;
                try {
                    result = c.call();
                    ran = true;
                } catch (Throwable ex) {
                    result = null;
                    ran = false;
                    setException(ex);
                }
                if (ran)
                    set(result);
            }
        } finally {
            // runner must be non-null until state is settled to
            // prevent concurrent calls to run()
            runner = null;
            // state must be re-read after nulling runner to prevent
            // leaked interrupts
            int s = state;
            if (s >= INTERRUPTING)
                handlePossibleCancellationInterrupt(s);
        }
    }

    /**
     * Executes the computation without setting its result, and then
     * resets this future to initial state, failing to do so if the
     * computation encounters an exception or is cancelled.  This is
     * designed for use with tasks that intrinsically execute more
     * than once.
     *
     * @return {@code true} if successfully run and reset
     */
    protected boolean runAndReset() {
        if (state != NEW ||
            !U.compareAndSwapObject(this, RUNNER, null, Thread.currentThread()))
            return false;
        boolean ran = false;
        int s = state;
        try {
            Callable<V> c = callable;
            if (c != null && s == NEW) {
                try {
                    c.call(); // don't set result
                    ran = true;
                } catch (Throwable ex) {
                    setException(ex);
                }
            }
        } finally {
            // runner must be non-null until state is settled to
            // prevent concurrent calls to run()
            runner = null;
            // state must be re-read after nulling runner to prevent
            // leaked interrupts
            s = state;
            if (s >= INTERRUPTING)
                handlePossibleCancellationInterrupt(s);
        }
        return ran && s == NEW;
    }

    /**
     * Ensures that any interrupt from a possible cancel(true) is only
     * delivered to a task while in run or runAndReset.
     */
    private void handlePossibleCancellationInterrupt(int s) {
        // It is possible for our interrupter to stall before getting a
        // chance to interrupt us.  Let's spin-wait patiently.
        if (s == INTERRUPTING)
            while (state == INTERRUPTING)
                Thread.yield(); // wait out pending interrupt

        // assert state == INTERRUPTED;

        // We want to clear any interrupt we may have received from
        // cancel(true).  However, it is permissible to use interrupts
        // as an independent mechanism for a task to communicate with
        // its caller, and there is no way to clear only the
        // cancellation interrupt.
        //
        // Thread.interrupted();
    }

    /**
     * Simple linked list nodes to record waiting threads in a Treiber
     * stack.  See other classes such as Phaser and SynchronousQueue
     * for more detailed explanation.
     */
    static final class WaitNode {
        volatile Thread thread;
        volatile WaitNode next;
        WaitNode() { thread = Thread.currentThread(); }
    }

    /**
     * Removes and signals all waiting threads, invokes done(), and
     * nulls out callable.
     */
    private void finishCompletion() {
        // assert state > COMPLETING;
        for (WaitNode q; (q = waiters) != null;) {
            if (U.compareAndSwapObject(this, WAITERS, q, null)) {
                for (;;) {
                    Thread t = q.thread;
                    if (t != null) {
                        q.thread = null;
                        LockSupport.unpark(t);
                    }
                    WaitNode next = q.next;
                    if (next == null)
                        break;
                    q.next = null; // unlink to help gc
                    q = next;
                }
                break;
            }
        }

        done();

        callable = null;        // to reduce footprint
    }

    /**
     * Awaits completion or aborts on interrupt or timeout.
     *
     * @param timed true if use timed waits
     * @param nanos time to wait, if timed
     * @return state upon completion or at timeout
     */
    private int awaitDone(boolean timed, long nanos)
        throws InterruptedException {
        // The code below is very delicate, to achieve these goals:
        // - call nanoTime exactly once for each call to park
        // - if nanos <= 0L, return promptly without allocation or nanoTime
        // - if nanos == Long.MIN_VALUE, don't underflow
        // - if nanos == Long.MAX_VALUE, and nanoTime is non-monotonic
        //   and we suffer a spurious wakeup, we will do no worse than
        //   to park-spin for a while
        long startTime = 0L;    // Special value 0L means not yet parked
        WaitNode q = null;
        boolean queued = false;
        for (;;) {
            int s = state;
            if (s > COMPLETING) {
                if (q != null)
                    q.thread = null;
                return s;
            }
            else if (s == COMPLETING)
                // We may have already promised (via isDone) that we are done
                // so never return empty-handed or throw InterruptedException
                Thread.yield();
            else if (Thread.interrupted()) {
                removeWaiter(q);
                throw new InterruptedException();
            }
            else if (q == null) {
                if (timed && nanos <= 0L)
                    return s;
                q = new WaitNode();
            }
            else if (!queued)
                queued = U.compareAndSwapObject(this, WAITERS,
                                                q.next = waiters, q);
            else if (timed) {
                final long parkNanos;
                if (startTime == 0L) { // first time
                    startTime = System.nanoTime();
                    if (startTime == 0L)
                        startTime = 1L;
                    parkNanos = nanos;
                } else {
                    long elapsed = System.nanoTime() - startTime;
                    if (elapsed >= nanos) {
                        removeWaiter(q);
                        return state;
                    }
                    parkNanos = nanos - elapsed;
                }
                // nanoTime may be slow; recheck before parking
                if (state < COMPLETING)
                    LockSupport.parkNanos(this, parkNanos);
            }
            else
                LockSupport.park(this);
        }
    }

    /**
     * Tries to unlink a timed-out or interrupted wait node to avoid
     * accumulating garbage.  Internal nodes are simply unspliced
     * without CAS since it is harmless if they are traversed anyway
     * by releasers.  To avoid effects of unsplicing from already
     * removed nodes, the list is retraversed in case of an apparent
     * race.  This is slow when there are a lot of nodes, but we don't
     * expect lists to be long enough to outweigh higher-overhead
     * schemes.
     */
    private void removeWaiter(WaitNode node) {
        if (node != null) {
            node.thread = null;
            retry:
            for (;;) {          // restart on removeWaiter race
                for (WaitNode pred = null, q = waiters, s; q != null; q = s) {
                    s = q.next;
                    if (q.thread != null)
                        pred = q;
                    else if (pred != null) {
                        pred.next = s;
                        if (pred.thread == null) // check for race
                            continue retry;
                    }
                    else if (!U.compareAndSwapObject(this, WAITERS, q, s))
                        continue retry;
                }
                break;
            }
        }
    }

    // Unsafe mechanics
    private static final sun.misc.Unsafe U = sun.misc.Unsafe.getUnsafe();
    private static final long STATE;
    private static final long RUNNER;
    private static final long WAITERS;
    static {
        try {
            STATE = U.objectFieldOffset
                (FutureTask.class.getDeclaredField("state"));
            RUNNER = U.objectFieldOffset
                (FutureTask.class.getDeclaredField("runner"));
            WAITERS = U.objectFieldOffset
                (FutureTask.class.getDeclaredField("waiters"));
        } catch (ReflectiveOperationException e) {
            throw new Error(e);
        }

        // Reduce the risk of rare disastrous classloading in first call to
        // LockSupport.park: https://bugs.openjdk.java.net/browse/JDK-8074773
        Class<?> ensureLoaded = LockSupport.class;
    }

}
```

Thanks