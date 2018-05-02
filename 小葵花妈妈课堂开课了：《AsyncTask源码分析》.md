**AsyncTask** 
官方介绍：

```
在UI线程使用AsyncTask是适当和简单的。
这个类允许你在UI线程中不使用多线程或者Handers的情况下，就能执行后台操作和发布结果。

AsyncTask是围绕Thread和Handler来设计的帮助类，不构成通用线程框架。
AsyncTasks通常理想情况下用来执行简短的操作（最多就是几秒钟）。
如果你需要保持线程跑很长时间，就推荐你使用java.util.concurrent包下面的Executor、ThreadPoolExecutor、FutureTask各种api。

一个异步任务是需要通过计算来定义，也就是任务跑在后台线程而结果会在UI线程发布。
异步任务定义有3个泛型类型，Prarams、Progress、Result；并且有4个步骤，叫做
onPreExecute、doInBackground、onProgressUpdate、onPostExecute

AsyncTask必须子类化才可以使用。子类需要至少重写1个方法doInBackground，并且通常需要重
写第二个方法onPostExecute

异步任务的三个泛型参数，Params这个参数是当执行任务是发送给task的参数；Progress是在后台计算
的进度回调返回的类型；Result是后台线程计算的返回值。

当一个异步任务执行的时候，task会经历4步：
1. onPreExecute，task执行之前，在UI线程调用。这步通常用来设置task，例如用来在用户的界面中展示一个进度条。
2. doInBackground，onPreExecute执行之后，立即在后台线程调用。
这步是用来调用后台计算，计算会花费较长时间。在这步中，异步任务的参数会传递进来。
计算的结果一定会在这步中返回回来，并且会传递到最后一步。
这步也会使用publishProgress用来发送一次或多次进度值。这些值会在主线程中通过onProgressUpdate这一步发布。
3. onProgressUpdate，在调用publishProgress之后在UI线程中调用onProgressUpdate。
执行的时机是不确定的。这个方法被用来在用户界面展示任意来自于执行中的内容。
例如被用来驱动一个进度条或者使用文本来展示日志。
4. onPostExecute，当后台线程执行完后在UI线程调用。后台计算的结果会在这一步中通过参数形式传递。

取消task
在任何时候都可以通过调用cancel(boolean)来取消task。
调用这个方法会造成，后续调用isCancelled()时返回true。
调用这个方法后,在执行完doInBackground(Object[])后，onCancelled(Object)会代替onPostExecute(Object)被调用。
确保任务尽可能快的被取消，比如在包含循环的情况下你应该经常定期在doInBackground(Object[])中检查isCancelled()的返回值。

Threading rules
在这个类中为了能确保正常工作，需要遵循一些线程规则：
	异步任务类必须要UI线程中进行加载。在android.os.Build.VERSION_CODES JELLY_BEAN 版本中自动完成了。
	task实例必须在UI线程中创建。
	execute方法一定要在UI线程中调用。
	不要手动调用onPreExecute、onPostExecute、doInBackground、onProgressUpdate。
	每一个task只能执行一次（如果当再次调用execution时，会抛出异常）。
	
Memory observability
AsyncTask通过该方法，下面操作在没有明确同步操作下是安全的，来保证所有的callback调用是同步的。
	在构造方法或者onPreExecute设置成员字段，并在doInBackground中进行引用。
	通过doInBackground设置成员变量，在onProgressUpdate和onPostExecute中进行引用。

Order of execution
当第一次对外公布时，AsyncTasks只会在一个后台线程中串行执行。
自从android.os.Build.VERSION_CODES DONUT 1.6后，变为使用一种允许并行操作多个任务的线程池执行。
在android.os.Build.VERSION_CODES HONEYCOMB 3.0.x后，任务被执行在单一线程中来避免一些因为并行引起的程序错误。
如果你想真正的并行执行，那么你需要使用结合THREAD_POOL_EXECUTOR去调用executeOnExecutor(java.util.concurrent.Executor, Object[])。
```
上面介绍了AsyncTask的使用方法，注意事项等。
下面是原文：
```
/**
 * <p>AsyncTask enables proper and easy use of the UI thread. This class allows you
 * to perform background operations and publish results on the UI thread without
 * having to manipulate threads and/or handlers.</p>
 *
 * <p>AsyncTask is designed to be a helper class around {@link Thread} and {@link Handler}
 * and does not constitute a generic threading framework. AsyncTasks should ideally be
 * used for short operations (a few seconds at the most.) If you need to keep threads
 * running for long periods of time, it is highly recommended you use the various APIs
 * provided by the <code>java.util.concurrent</code> package such as {@link Executor},
 * {@link ThreadPoolExecutor} and {@link FutureTask}.</p>
 *
 * <p>An asynchronous task is defined by a computation that runs on a background thread and
 * whose result is published on the UI thread. An asynchronous task is defined by 3 generic
 * types, called <code>Params</code>, <code>Progress</code> and <code>Result</code>,
 * and 4 steps, called <code>onPreExecute</code>, <code>doInBackground</code>,
 * <code>onProgressUpdate</code> and <code>onPostExecute</code>.</p>
 *
 * <div class="special reference">
 * <h3>Developer Guides</h3>
 * <p>For more information about using tasks and threads, read the
 * <a href="{@docRoot}guide/components/processes-and-threads.html">Processes and
 * Threads</a> developer guide.</p>
 * </div>
 *
 * <h2>Usage</h2>
 * <p>AsyncTask must be subclassed to be used. The subclass will override at least
 * one method ({@link #doInBackground}), and most often will override a
 * second one ({@link #onPostExecute}.)</p>
 *
 * <p>Here is an example of subclassing:</p>
 * <pre class="prettyprint">
 * private class DownloadFilesTask extends AsyncTask&lt;URL, Integer, Long&gt; {
 *     protected Long doInBackground(URL... urls) {
 *         int count = urls.length;
 *         long totalSize = 0;
 *         for (int i = 0; i < count; i++) {
 *             totalSize += Downloader.downloadFile(urls[i]);
 *             publishProgress((int) ((i / (float) count) * 100));
 *             // Escape early if cancel() is called
 *             if (isCancelled()) break;
 *         }
 *         return totalSize;
 *     }
 *
 *     protected void onProgressUpdate(Integer... progress) {
 *         setProgressPercent(progress[0]);
 *     }
 *
 *     protected void onPostExecute(Long result) {
 *         showDialog("Downloaded " + result + " bytes");
 *     }
 * }
 * </pre>
 *
 * <p>Once created, a task is executed very simply:</p>
 * <pre class="prettyprint">
 * new DownloadFilesTask().execute(url1, url2, url3);
 * </pre>
 *
 * <h2>AsyncTask's generic types</h2>
 * <p>The three types used by an asynchronous task are the following:</p>
 * <ol>
 *     <li><code>Params</code>, the type of the parameters sent to the task upon
 *     execution.</li>
 *     <li><code>Progress</code>, the type of the progress units published during
 *     the background computation.</li>
 *     <li><code>Result</code>, the type of the result of the background
 *     computation.</li>
 * </ol>
 * <p>Not all types are always used by an asynchronous task. To mark a type as unused,
 * simply use the type {@link Void}:</p>
 * <pre>
 * private class MyTask extends AsyncTask&lt;Void, Void, Void&gt; { ... }
 * </pre>
 *
 * <h2>The 4 steps</h2>
 * <p>When an asynchronous task is executed, the task goes through 4 steps:</p>
 * <ol>
 *     <li>{@link #onPreExecute()}, invoked on the UI thread before the task
 *     is executed. This step is normally used to setup the task, for instance by
 *     showing a progress bar in the user interface.</li>
 *     <li>{@link #doInBackground}, invoked on the background thread
 *     immediately after {@link #onPreExecute()} finishes executing. This step is used
 *     to perform background computation that can take a long time. The parameters
 *     of the asynchronous task are passed to this step. The result of the computation must
 *     be returned by this step and will be passed back to the last step. This step
 *     can also use {@link #publishProgress} to publish one or more units
 *     of progress. These values are published on the UI thread, in the
 *     {@link #onProgressUpdate} step.</li>
 *     <li>{@link #onProgressUpdate}, invoked on the UI thread after a
 *     call to {@link #publishProgress}. The timing of the execution is
 *     undefined. This method is used to display any form of progress in the user
 *     interface while the background computation is still executing. For instance,
 *     it can be used to animate a progress bar or show logs in a text field.</li>
 *     <li>{@link #onPostExecute}, invoked on the UI thread after the background
 *     computation finishes. The result of the background computation is passed to
 *     this step as a parameter.</li>
 * </ol>
 * 
 * <h2>Cancelling a task</h2>
 * <p>A task can be cancelled at any time by invoking {@link #cancel(boolean)}. Invoking
 * this method will cause subsequent calls to {@link #isCancelled()} to return true.
 * After invoking this method, {@link #onCancelled(Object)}, instead of
 * {@link #onPostExecute(Object)} will be invoked after {@link #doInBackground(Object[])}
 * returns. To ensure that a task is cancelled as quickly as possible, you should always
 * check the return value of {@link #isCancelled()} periodically from
 * {@link #doInBackground(Object[])}, if possible (inside a loop for instance.)</p>
 *
 * <h2>Threading rules</h2>
 * <p>There are a few threading rules that must be followed for this class to
 * work properly:</p>
 * <ul>
 *     <li>The AsyncTask class must be loaded on the UI thread. This is done
 *     automatically as of {@link android.os.Build.VERSION_CODES#JELLY_BEAN}.</li>
 *     <li>The task instance must be created on the UI thread.</li>
 *     <li>{@link #execute} must be invoked on the UI thread.</li>
 *     <li>Do not call {@link #onPreExecute()}, {@link #onPostExecute},
 *     {@link #doInBackground}, {@link #onProgressUpdate} manually.</li>
 *     <li>The task can be executed only once (an exception will be thrown if
 *     a second execution is attempted.)</li>
 * </ul>
 *
 * <h2>Memory observability</h2>
 * <p>AsyncTask guarantees that all callback calls are synchronized in such a way that the following
 * operations are safe without explicit synchronizations.</p>
 * <ul>
 *     <li>Set member fields in the constructor or {@link #onPreExecute}, and refer to them
 *     in {@link #doInBackground}.
 *     <li>Set member fields in {@link #doInBackground}, and refer to them in
 *     {@link #onProgressUpdate} and {@link #onPostExecute}.
 * </ul>
 *
 * <h2>Order of execution</h2>
 * <p>When first introduced, AsyncTasks were executed serially on a single background
 * thread. Starting with {@link android.os.Build.VERSION_CODES#DONUT}, this was changed
 * to a pool of threads allowing multiple tasks to operate in parallel. Starting with
 * {@link android.os.Build.VERSION_CODES#HONEYCOMB}, tasks are executed on a single
 * thread to avoid common application errors caused by parallel execution.</p>
 * <p>If you truly want parallel execution, you can invoke
 * {@link #executeOnExecutor(java.util.concurrent.Executor, Object[])} with
 * {@link #THREAD_POOL_EXECUTOR}.</p>
 */
```


----------


下面进行源码分析：
还是老规矩，通过成员变量入手，

```
//cpu可用数量
private static final int CPU_COUNT = Runtime.getRuntime().availableProcessors();
// We want at least 2 threads and at most 4 threads in the core pool,
// preferring to have 1 less than the CPU count to avoid saturating
// the CPU with background work
// 我们想要在线程池中至少有2个线程和最多4个线程，宁愿比cpu数量少1，以此来避免因为后台工作引起的饱和。

//保存在池中的线程数
private static final int CORE_POOL_SIZE = Math.max(2, Math.min(CPU_COUNT - 1, 4));
//池中允许的最大线程数
private static final int MAXIMUM_POOL_SIZE = CPU_COUNT * 2 + 1;
//当线程数大于cpu核数的时候，KEEP_ALIVE_SECONDS该时间为空余线程在终止之前等待新任务的最大时间。
private static final int KEEP_ALIVE_SECONDS = 30;

//用来创建新线程
private static final ThreadFactory sThreadFactory = new ThreadFactory() {
    private final AtomicInteger mCount = new AtomicInteger(1);

    public Thread newThread(Runnable r) {
        return new Thread(r, "AsyncTask #" + mCount.getAndIncrement());
    }
};

//在task被执行之前，存储在sPoolWorkQueue 队列中。该队列进存放通过execute方法添加的Runnable任务。
private static final BlockingQueue<Runnable> sPoolWorkQueue =
        new LinkedBlockingQueue<Runnable>(128);

/**
 * An {@link Executor} that can be used to execute tasks in parallel.
 */
public static final Executor THREAD_POOL_EXECUTOR;

static {
	//上面都是准备工作，准备构造ThreadPoolExecutor。
    ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
            CORE_POOL_SIZE, MAXIMUM_POOL_SIZE, KEEP_ALIVE_SECONDS, TimeUnit.SECONDS,
            sPoolWorkQueue, sThreadFactory);
    //线程池数量最后销毁到0个
    threadPoolExecutor.allowCoreThreadTimeOut(true);
    THREAD_POOL_EXECUTOR = threadPoolExecutor;
}

/**
 * An {@link Executor} that executes tasks one at a time in serial
 * order.  This serialization is global to a particular process.
 */
//SERIAL_EXECUTOR 是顺序执行task的。在指定进程中是全局的。
public static final Executor SERIAL_EXECUTOR = new SerialExecutor();

private static final int MESSAGE_POST_RESULT = 0x1;
private static final int MESSAGE_POST_PROGRESS = 0x2;

//执行task的Executor，默认为SerialExecutor，后面介绍SerialExecutor类
private static volatile Executor sDefaultExecutor = SERIAL_EXECUTOR;

//通过sHandler将后台线程返回的结果转到主线程
private static InternalHandler sHandler;

//mWorker在AsyncTask的构造方法中创建
private final WorkerRunnable<Params, Result> mWorker;
private final FutureTask<Result> mFuture;

private volatile Status mStatus = Status.PENDING;

//用来同步线程是否被取消，或者执行失败
private final AtomicBoolean mCancelled = new AtomicBoolean();
//用来同步task是否被调用
private final AtomicBoolean mTaskInvoked = new AtomicBoolean();

//Executor, 默认的Executor，按顺序一个个执行。在制定进程中是全局的。
private static class SerialExecutor implements Executor {
    final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
    Runnable mActive;

    public synchronized void execute(final Runnable r) {
        mTasks.offer(new Runnable() {
            public void run() {
                try {
                    r.run();
                } finally {
                    //执行完一个，顺序执行下一个。
                    scheduleNext();
                }
            }
        });
        if (mActive == null) {
            //如果mTasks 中没有任务执行那么将任务通过THREAD_POOL_EXECUTOR执行。
            scheduleNext();
        }
    }

    protected synchronized void scheduleNext() {
        if ((mActive = mTasks.poll()) != null) {
            //通过THREAD_POOL_EXECUTOR执行
            THREAD_POOL_EXECUTOR.execute(mActive);
        }
    }
}

/**
 * Indicates the current status of the task. Each status will be set only once
 * during the lifetime of a task.
 */
 //表示当前任务状态。在task的生命周期内每一个状态仅能被设置一次。
public enum Status {
    /**
     * Indicates that the task has not been executed yet.
     */
     //标示task还没有被执行
    PENDING,
    /**
     * Indicates that the task is running.
     */
     //task正在执行
    RUNNING,
    /**
     * Indicates that {@link AsyncTask#onPostExecute} has finished.
     */
     //onPostExecute已经执行完成。
    FINISHED,
}

/** @hide */
//更换sDefaultExecutor ，为hide方法，默认不对开发者提供，默认使用SerialExecutor
public static void setDefaultExecutor(Executor exec) {
    sDefaultExecutor = exec;
}

/**
 * Creates a new asynchronous task. This constructor must be invoked on the UI thread.
 */
 //创建一个新的异步任务。该构造方法一定要在UI线程调用。
public AsyncTask() {
    mWorker = new WorkerRunnable<Params, Result>() {
        public Result call() throws Exception {
            //标志该任务已经被调用
            mTaskInvoked.set(true);
            Result result = null;
            try {
                //设置当前线程优先级，
                Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
                //noinspection unchecked
                //传递mParams，并执行doInBackground()
                result = doInBackground(mParams);
                //官方解释：flush在当前线程任何等待的Binder命令到内核驱动程序。调用这个函数是有益的
                //在执行一个可能阻塞很长时间的操作。为了确保任何等待的object引用会被释放,
                //为了防止进程对对象持有超过它需要的时间
                
                //翻译的不是很好， 查阅了一些资料
                //Binder.flushPendingCommands()方法被调用说明后面的代码可能会引起线程阻塞
                Binder.flushPendingCommands();
            } catch (Throwable tr) {
                //当有异常时，设置mCancelled为true,说明被取消
                mCancelled.set(true);
                throw tr;
            } finally {
                //通过主线程返回结果
                postResult(result);
            }
            return result;
        }
    };

    //mWorker 会放在FutureTask中执行。
    mFuture = new FutureTask<Result>(mWorker) {
        @Override
        protected void done() {
            //如果task没有调用，通过postResultIfNotInvoked，返回结果
            try {
                //get()会引起阻塞，等待计算结果
                postResultIfNotInvoked(get());
            } catch (InterruptedException e) {
                android.util.Log.w(LOG_TAG, e);
            } catch (ExecutionException e) {
                throw new RuntimeException("An error occurred while executing doInBackground()",
                        e.getCause());
            } catch (CancellationException e) {
                postResultIfNotInvoked(null);
            }
        }
    };
}
private void postResultIfNotInvoked(Result result) {
    final boolean wasTaskInvoked = mTaskInvoked.get();
    //如果task没有调用，通过postResultIfNotInvoked，返回结果
    if (!wasTaskInvoked) {
        postResult(result);
    }
}
private Result postResult(Result result) {
    //最终通过该方法返回结果，无论task是否执行。
    //getHandler会在主线中执行MESSAGE_POST_RESULT case 返回result
    @SuppressWarnings("unchecked")
    Message message = getHandler().obtainMessage(MESSAGE_POST_RESULT,
            new AsyncTaskResult<Result>(this, result));
    message.sendToTarget();
    return result;
}

private static class InternalHandler extends Handler {
    public InternalHandler() {
        //绑定到主线程的Looper
        super(Looper.getMainLooper());
    }

    @SuppressWarnings({"unchecked", "RawUseOfParameterizedType"})
    @Override
    public void handleMessage(Message msg) {
        AsyncTaskResult<?> result = (AsyncTaskResult<?>) msg.obj;
        switch (msg.what) {
            case MESSAGE_POST_RESULT:
                // There is only one result
                // 在主线程执行finish()
                result.mTask.finish(result.mData[0]);
                break;
            case MESSAGE_POST_PROGRESS:
                // 在主线程回调onProgressUpdate
                result.mTask.onProgressUpdate(result.mData);
                break;
        }
    }
}

private void finish(Result result) {
    if (isCancelled()) {
        //如果task被取消，调用onCancelled(),不会调用onPostExecute
        onCancelled(result);
    } else {
        onPostExecute(result);
    }
    //更新task状态
    mStatus = Status.FINISHED;
}
```

下面介绍一下几个子类需要复写的方法

```
/**
 * Override this method to perform a computation on a background thread. The
 * specified parameters are the parameters passed to {@link #execute}
 * by the caller of this task.
 *
 * This method can call {@link #publishProgress} to publish updates
 * on the UI thread.
 *
 * @param params The parameters of the task.
 *
 * @return A result, defined by the subclass of this task.
 *
 * @see #onPreExecute()
 * @see #onPostExecute
 * @see #publishProgress
 */
 // 复写该方法，在后台线程中执行计算。通过调用execute传递的指定的参数 params
 // 该方法可以在主线程调用publishProgress来更新进度
 // 返回一个result。类型由task子类定义的类型。
@WorkerThread
protected abstract Result doInBackground(Params... params);

/**
 * Runs on the UI thread before {@link #doInBackground}.
 *
 * @see #onPostExecute
 * @see #doInBackground
 */
 // 在调用doInBackground之前，首先在主线程调用onPreExecute
 // 在子线程计算之前，需要在主线程准备一些工作。就复写该方法在这里面完成
@MainThread
protected void onPreExecute() {
}

/**
 * <p>Runs on the UI thread after {@link #doInBackground}. The
 * specified result is the value returned by {@link #doInBackground}.</p>
 * 
 * <p>This method won't be invoked if the task was cancelled.</p>
 *
 * @param result The result of the operation computed by {@link #doInBackground}.
 *
 * @see #onPreExecute
 * @see #doInBackground
 * @see #onCancelled(Object) 
 */
 // 当doInBackground执行完成后，会在主线程中调用onPostExecute方法。参数result是
 // doInBackground返回的。
 // 如果task被取消了，那么就不会调用onPostExecute返回结果。而是通过onCancelled
@SuppressWarnings({"UnusedDeclaration"})
@MainThread
protected void onPostExecute(Result result) {
}

/**
 * Runs on the UI thread after {@link #publishProgress} is invoked.
 * The specified values are the values passed to {@link #publishProgress}.
 *
 * @param values The values indicating progress.
 *
 * @see #publishProgress
 * @see #doInBackground
 */
 // 调用publishProgress之后，在主线程调用onProgressUpdate。
 // values参数是publishProgress传递过来的。
@SuppressWarnings({"UnusedDeclaration"})
@MainThread
protected void onProgressUpdate(Progress... values) {
}

/**
 * <p>Runs on the UI thread after {@link #cancel(boolean)} is invoked and
 * {@link #doInBackground(Object[])} has finished.</p>
 * 
 * <p>The default implementation simply invokes {@link #onCancelled()} and
 * ignores the result. If you write your own implementation, do not call
 * <code>super.onCancelled(result)</code>.</p>
 *
 * @param result The result, if any, computed in
 *               {@link #doInBackground(Object[])}, can be null
 * 
 * @see #cancel(boolean)
 * @see #isCancelled()
 */
 // cancel调用之后，会在主线程调用onCancelled方法。并且doInBackground已经执行完成。
 // 默认实现简单的调用调用了onCancelled()，并且忽略result。
 // 如果写了自己的实现，那么就不需要调用super.onCancelled(result)。
 // result 为doInBackground计算的结果，可能为null
@SuppressWarnings({"UnusedParameters"})
@MainThread
protected void onCancelled(Result result) {
    onCancelled();
}    

/**
 * <p>Applications should preferably override {@link #onCancelled(Object)}.
 * This method is invoked by the default implementation of
 * {@link #onCancelled(Object)}.</p>
 * 
 * <p>Runs on the UI thread after {@link #cancel(boolean)} is invoked and
 * {@link #doInBackground(Object[])} has finished.</p>
 *
 * @see #onCancelled(Object) 
 * @see #cancel(boolean)
 * @see #isCancelled()
 */
 // 最好复写onCancelled(object)方法。这个方法被默认实现的onCancelled(Object)方法所调用。
 // 在cancel(boolean)运行之后并且doInBackground(Object[])也调用完成之后，在主线程调用onCancelled()
@MainThread
protected void onCancelled() {
}
```
大概分析到这。

总结一下：

1. 对外提供的方法，需要复写的方法除了doInBackground外都是在主线程回调。
2. 在线程池中线程最多可存在数量为CPU_COUNT * 2 + 1， CPU_COUNT至少为2最多为4
3. 使用get或者计算结果，会星辰阻塞。还有可能有异常返回
4. 一个task只能执行一次

Thanks.
