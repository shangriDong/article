# Handler Looper Message Thread

首先要阐述几者之间的关系。
Thread 可以拥有多个handler对象；
Thread 只能拥有一个Looper 和一个MessageQueue。

Looper 只能属于一个Thread， 并且只能和MessageQueue 一一对应。 looper的在几者中的作用是什么呢！
Looper的作用就是起到 发动机的原理，当然它不是让车跑起来，而是让MessageQueue里的message被执行。
那么 Message被谁执行呢？ 后文即会提到。


MessageQueue 也仅是和一个looper绑定，在出生的时候即决定了这件事，后面在代码中会解释为什么！
MessageQueue里面存放就是 Message。


**Looper**

首先需要关注的是该方法。
```
public static void prepare() {
    prepare(true);
}
```
参数为是否允许退出，答案是肯定的 true;  只有一种情况即主线程调用prepare时传递false,因为主线程不允许退出。
该方法即为 预热发动机的入口。让 Looper这台机器进行启动之前的准备工作。
```
private static void prepare(boolean quitAllowed) {
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}
```

分析一下 是如何判断已经prepare的呢？
sThreadLocal.get() != null
那就需要看一下set是什么东东。就是准备的是什么呢？

```
/**
 * Sets the current thread's copy of this thread-local variable
 * to the specified value.  Most subclasses will have no need to
 * override this method, relying solely on the {@link #initialValue}
 * method to set the values of thread-locals.
 *
 * @param value the value to be stored in the current thread's copy of
 *        this thread-local.
 */
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}
```
这个value就是上文提到的 new Looper(quitAllowed)
createMap创建的是一个ThreadLocalMap。
每一个线程仅有一个ThreadLocalMap， 在该map中存储内容为该线程本地变量的副本。ThreadLocalMap使用及注意事项以后单独开讲。
```
void createMap(Thread t, T firstValue) {
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```

 当第一次sThreadLocal.get()时，会返回setInitialValue=null;

```
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);
    mThread = Thread.currentThread();
}
```
当一个线程只能有一个Looper之后也就意味着只能有一个MessageQueue.class

Looper.loop即为启动发动机的入口，启动之后开始进行消息轮询，并且注释说明一定要调用quit()退出轮询。
Looper一直把MesageQueue所有的message执行完。
每执行完一个后即通过next拿到下一个message.
```
public static void loop() {
---
for (;;) {
        Message msg = queue.next(); // might block
        if (msg == null) {
            // No message indicates that the message queue is quitting.
            return;
        } //当队列中没有消息之后 即退出。

        // This must be in a local variable, in case a UI event sets the logger
        final Printer logging = me.mLogging;
        if (logging != null) {
            logging.println(">>>>> Dispatching to " + msg.target + " " +
                    msg.callback + ": " + msg.what);
        }

        final long traceTag = me.mTraceTag;
        if (traceTag != 0 && Trace.isTagEnabled(traceTag)) {
            Trace.traceBegin(traceTag, msg.target.getTraceName(msg));
        }
        try {
            msg.target.dispatchMessage(msg); //msg.target即为执行message工具。
        } finally {
            if (traceTag != 0) {
                Trace.traceEnd(traceTag);
            }
        }

        if (logging != null) {
            logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
        }

        // Make sure that during the course of dispatching the
        // identity of the thread wasn't corrupted.
        final long newIdent = Binder.clearCallingIdentity();
        if (ident != newIdent) {
            Log.wtf(TAG, "Thread identity changed from 0x"
                    + Long.toHexString(ident) + " to 0x"
                    + Long.toHexString(newIdent) + " while dispatching to "
                    + msg.target.getClass().getName() + " "
                    + msg.callback + " what=" + msg.what);
        }

        msg.recycleUnchecked();
    }
}
```

**MessageQueue**
MessageQueue和Looper之间有个紧密的联系就是通过 MessageQueue.next()方法。以next方法为切入点介绍MessageQueue.class

```
Message next() {
    // Return here if the message loop has already quit and been disposed.
    // This can happen if the application tries to restart a looper after quit
    // which is not supported.
    final long ptr = mPtr;
    if (ptr == 0) {
        return null;
    }

    int pendingIdleHandlerCount = -1; // -1 only during first iteration
    int nextPollTimeoutMillis = 0;
    for (;;) {
        if (nextPollTimeoutMillis != 0) {
            Binder.flushPendingCommands();
        }

        //natvie层进行阻塞,后文在Looper.c中介绍
        nativePollOnce(ptr, nextPollTimeoutMillis);

        synchronized (this) {
            // Try to retrieve the next message.  Return if found.
            final long now = SystemClock.uptimeMillis();
            Message prevMsg = null;
            Message msg = mMessages;
            if (msg != null && msg.target == null) {
                // Stalled by a barrier.  Find the next asynchronous message in the queue.
                // 当因为有 "同步分隔栏" 引起停滞后， 将要找到下一个异步消息， 
                // 同步分隔栏后面的同步消息并不会执行
                do {
                    prevMsg = msg;
                    msg = msg.next;
                } while (msg != null && !msg.isAsynchronous());
            }
            if (msg != null) {
                if (now < msg.when) {
                    // Next message is not ready.  Set a timeout to wake up when it is ready.
                    //如果当前的msg没有准备好，那么就下次轮询进入到等待。
                    //计算等待时间
                    nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                } else {
                    // Got a message.
                    //标记当次轮询不会被wait,不需要被唤醒
                    mBlocked = false;
                    //当在链表队列中找到可执行msg,把当前message调出，并修复原链接
                    if (prevMsg != null) {
                        prevMsg.next = msg.next;
                    } else {
                        mMessages = msg.next;
                    }
                    msg.next = null;
                    if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                    //标记当前msg被使用状态
                    msg.markInUse();
                    return msg;
                }
            } else {
                // No more messages.
                nextPollTimeoutMillis = -1;
            }

            // Process the quit message now that all pending messages have been handled.
            //如果looper调用了quit, messagequeue也进行退出操作。
            if (mQuitting) {
                dispose();
                return null;
            }

            // If first time idle, then get the number of idlers to run.
            // Idle handles only run if the queue is empty or if the first message
            // in the queue (possibly a barrier) is due to be handled in the future.
            // 引入了另外一个messagequeue的功能, idle handles的处理，
            // 当队列为空的时候或没有任务可执行的时候，执行idle handles内容。
            if (pendingIdleHandlerCount < 0
                    && (mMessages == null || now < mMessages.when)) {
                pendingIdleHandlerCount = mIdleHandlers.size();
            }
            if (pendingIdleHandlerCount <= 0) {
                // No idle handlers to run.  Loop and wait some more.
                // 既没有idle handlers 和message可以处理那么就需要阻塞,入队时候就需要唤醒。
                mBlocked = true;
                continue;
            }

            if (mPendingIdleHandlers == null) {
                mPendingIdleHandlers = new IdleHandler[Math.max(pendingIdleHandlerCount, 4)];
            }
            mPendingIdleHandlers = mIdleHandlers.toArray(mPendingIdleHandlers);
        }

        // Run the idle handlers.
        // We only ever reach this code block during the first iteration.
        for (int i = 0; i < pendingIdleHandlerCount; i++) {
            final IdleHandler idler = mPendingIdleHandlers[i];
            mPendingIdleHandlers[i] = null; // release the reference to the handler

            boolean keep = false;
            try {
                //执行idler中的回调，并且有返回值，true意味着想要保持这个idle下次继续执行，
                //false则会从队列中移除
                keep = idler.queueIdle();
            } catch (Throwable t) {
                Log.wtf(TAG, "IdleHandler threw exception", t);
            }

            if (!keep) {
                synchronized (this) {
                    mIdleHandlers.remove(idler);
                }
            }
        }

        // Reset the idle handler count to 0 so we do not run them again.
        pendingIdleHandlerCount = 0;

        // While calling an idle handler, a new message could have been delivered
        // so go back and look again for a pending message without waiting.
        nextPollTimeoutMillis = 0;
    }
}
```
下面继续介绍enqueueMessage，入队操作由Handler.class执行。后文提到其中几种入队操作。

```
boolean enqueueMessage(Message msg, long when) {
 if (msg.target == null) {
        throw new IllegalArgumentException("Message must have a target.");
    }
    if (msg.isInUse()) {
        throw new IllegalStateException(msg + " This message is already in use.");
    }

    synchronized (this) {
	    //如果looper已经条用quit，那么就放弃入队。
        if (mQuitting) {
            IllegalStateException e = new IllegalStateException(
                    msg.target + " sending message to a Handler on a dead thread");
            Log.w(TAG, e.getMessage(), e);
            msg.recycle();
            return false;
        }

        msg.markInUse();
        msg.when = when;
        Message p = mMessages;
        boolean needWake;
        if (p == null || when == 0 || when < p.when) {
            // New head, wake up the event queue if blocked.
            // 如果messagequeue中没有message或者需要立即执行或者插入message时间优于对头
            // message所需要执行时间，那么就把msg插入到对头。
            msg.next = p;
            mMessages = msg;
            needWake = mBlocked;
        } else {
            // Inserted within the middle of the queue.  Usually we don't have to wake
            // up the event queue unless there is a barrier at the head of the queue
            // and the message is the earliest asynchronous message in the queue.
            //通常情况下将目标message插入到队里中间时，是不需要唤醒队列的，
            //除非有一个"同步分隔栏"在对头或者目标message是最早需要执行的异步message。
            needWake = mBlocked && p.target == null && msg.isAsynchronous();
            Message prev;
            for (;;) {
                prev = p;
                p = p.next;
                if (p == null || when < p.when) {
	                //找到最后一个位置，或者时间排序上晚于目标message的位置
                    break;
                }
                //当需要唤醒，但是 要插入目标message的前面所有位置的message
                //只要有异步消息的话既不需要唤醒。
                if (needWake && p.isAsynchronous()) {
                    needWake = false;
                }
            }
            // 将目标message插入到理想位置，修复整个链接
            msg.next = p; // invariant: p == prev.next
            prev.next = msg;
        }

        // We can assume mPtr != 0 because mQuitting is false.
        if (needWake) {
	        //此处为唤醒 Looper
            nativeWake(mPtr);
        }
    }
    return true;
}
```
上面介绍了MessageQueue的两个主要方法next()和enqueueMessage()，其中涉及到了两个native层的本地方法分别为：
nativePollOnce(ptr, nextPollTimeoutMillis);
nativeWake(mPtr);
那么下面介绍一下这两个方法。
方法在/frameworks/base/core/jni/android_os_MessageQueue.cpp中进行了定义。

```
static JNINativeMethod gMessageQueueMethods[] = {
    /* name, signature, funcPtr */
    { "nativeInit", "()V", (void*)android_os_MessageQueue_nativeInit },
    { "nativeDestroy", "()V", (void*)android_os_MessageQueue_nativeDestroy },
    { "nativePollOnce", "(II)V", (void*)android_os_MessageQueue_nativePollOnce },
    { "nativeWake", "(I)V", (void*)android_os_MessageQueue_nativeWake }
};
```

```
static void android_os_MessageQueue_nativePollOnce(JNIEnv* env, jobject obj,
        jint ptr, jint timeoutMillis) {
    NativeMessageQueue* nativeMessageQueue = reinterpret_cast<NativeMessageQueue*>(ptr);
    nativeMessageQueue->pollOnce(timeoutMillis);
}
```
最终调用到Looper::pollOnce====>Looper::pollInner

```
int Looper::pollOnce(int timeoutMillis, int* outFd, int* outEvents, void** outData);
int Looper::pollInner(int timeoutMillis);
```


```
int Looper::pollInner(int timeoutMillis) {
    ---
#ifdef LOOPER_USES_EPOLL
    struct epoll_event eventItems[EPOLL_MAX_EVENTS];
    //通过Epoll进行阻塞
    int eventCount = epoll_wait(mEpollFd, eventItems, EPOLL_MAX_EVENTS, timeoutMillis);
#else
    // Wait for wakeAndLock() waiters to run then set mPolling to true.
    mLock.lock();
    while (mWaiters != 0) {
        mResume.wait(mLock);
    }
    mPolling = true;
    mLock.unlock();

    size_t requestedCount = mRequestedFds.size();
    int eventCount = poll(mRequestedFds.editArray(), requestedCount, timeoutMillis);
#endif
    ---
}

```
其中最终运用epoll进行控制（epoll不再本文讨论感兴趣读者可自行查询！
）。下面引入《深入理解Android：卷II》对pollOnce解释：

>其中四个参数：
timeoutMillis参数为超时等待时间。如果值为–1，则表示无限等待，直到有事件发生为止。如果值为0，则无须等待立即返回。
outFd用来存储发生事件的那个文件描述符。
outEvents用来存储在该文件描述符上发生了哪些事件，目前支持可读、可写、错误和中断4个事件。这4个事件其实是从epoll事件转化而来的。后面我们会介绍大名鼎鼎的epoll。
outData用于存储上下文数据，这个上下文数据是由用户在添加监听句柄时传递的，它的作用和pthread_create函数最后一个参数param一样，用来传递用户自定义的数据。
另外，pollOnce函数的返回值也具有特殊的意义，具体如下：
当返回值为ALOOPER_POLL_WAKE时，表示这次返回是由wake函数触发的，也就是管道写端的那次写事件触发的。
返回值为ALOOPER_POLL_TIMEOUT表示等待超时。
返回值为ALOOPER_POLL_ERROR表示等待过程中发生错误。
返回值为ALOOPER_POLL_CALLBACK表示某个被监听的句柄因某种原因被触发。这时，outFd参数用于存储发生事件的文件句柄，outEvents用于存储所发生的事件。

MessageQueue还有其他公开方法：

用来添加IdleHandler，当没有message需要立即处理时就会处理IdleHandler。
```
void addIdleHandler(@NonNull IdleHandler handler);
void removeIdleHandler(@NonNull IdleHandler handler);
```

用来添加需要监听的文件描述符fd
```
void addOnFileDescriptorEventListener(@NonNull FileDescriptor fd,
            @OnFileDescriptorEventListener.Events int events,
            @NonNull OnFileDescriptorEventListener listener);
void removeOnFileDescriptorEventListener(@NonNull FileDescriptor fd);
```

**Message.class**
主要是handler要处理的信使，主要功能携带参数。下面主要介绍handler参数。

```
/**
 * User-defined message code so that the recipient can identify 
 * what this message is about. Each {@link Handler} has its own name-space
 * for message codes, so you do not need to worry about yours conflicting
 * with other handlers.
 */
public int what;
//定义在handler中要执行的事件

/**
 * arg1 and arg2 are lower-cost alternatives to using
 * {@link #setData(Bundle) setData()} if you only need to store a
 * few integer values.
 */
public int arg1; 
//如果要存储简单的参数，使用arg1和arg2就可以

/**
 * arg1 and arg2 are lower-cost alternatives to using
 * {@link #setData(Bundle) setData()} if you only need to store a
 * few integer values.
 */
public int arg2;

/**
 * An arbitrary object to send to the recipient.  When using
 * {@link Messenger} to send the message across processes this can only
 * be non-null if it contains a Parcelable of a framework class (not one
 * implemented by the application).   For other data transfer use
 * {@link #setData}.
 * 
 * <p>Note that Parcelable objects here are not supported prior to
 * the {@link android.os.Build.VERSION_CODES#FROYO} release.
 */
public Object obj;
//可存储任意类型参数

/**
 * Optional Messenger where replies to this message can be sent.  The
 * semantics of exactly how this is used are up to the sender and
 * receiver.
 */
public Messenger replyTo;
//可实现跨进程通信，后面会独立章节进行讲解。

/**
 * Optional field indicating the uid that sent the message.  This is
 * only valid for messages posted by a {@link Messenger}; otherwise,
 * it will be -1.
 */
public int sendingUid = -1;
//与Messenger 配合使用

/*package*/ int flags;
//0x00 非使用， 0x01被使用：当入队和被回收的时候会设置为1
//0x10 表示为异步

/*package*/ long when;
//延迟执行时间

/*package*/ Bundle data;
//存储一些复杂数据

/*package*/ Handler target;
//执行该message的handler

/*package*/ Runnable callback;
//hanlder执行该message时，如果有callback即执行该callback

// sometimes we store linked lists of these things
/*package*/ Message next;
//保存链表
```
主要解析一下该函数

```
/**
* Return a new Message instance from the global pool. Allows us to
 * avoid allocating new objects in many cases.
 */
public static Message obtain() {
	//sPoolSync 同步锁
    synchronized (sPoolSync) {
	    //sPool指向链表的头
        if (sPool != null) {
            Message m = sPool;
            sPool = m.next;
            m.next = null;
            m.flags = 0; // clear in-use flag
            sPoolSize--;
            //将sPool取出，并断链
            return m;
        }
    }
    //如果链中没有元素，重新分配
    return new Message();
}

/**
 * Recycles a Message that may be in-use.
 * Used internally by the MessageQueue and Looper when disposing of queued Messages.
 */
void recycleUnchecked() {
    // Mark the message as in use while it remains in the recycled object pool.
    // Clear out all other details.
    flags = FLAG_IN_USE;
    what = 0;
    arg1 = 0;
    arg2 = 0;
    obj = null;
    replyTo = null;
    sendingUid = -1;
    when = 0;
    target = null;
    callback = null;
    data = null;

    synchronized (sPoolSync) {
	    //链表不会无限增长
        if (sPoolSize < MAX_POOL_SIZE) {
            next = sPool;
            sPool = this;
            sPoolSize++;
            //将该message插入头部
        }
    }
}
```

**Handler**
先比较前几个Class， Handler比较简单，成员只有以下几个：
```
final Looper mLooper;
final MessageQueue mQueue;
final Callback mCallback;
final boolean mAsynchronous;
IMessenger mMessenger;
```
先看几个比较重要的构造方法：

```
//常用的为无参构造形式
public Handler() {
    this(null, false);
}

//这是无参构造方法调用的真正构造方法， 
public Handler(Callback callback, boolean async) {
	//FIND_POTENTIAL_LEAKS
	//将此标志设置为true以检测扩展的Handler类, 扩展的handler类如果不是静态的匿名,本地或成员类, 
    //则会产生泄漏。我们常见构造时的警告说明!至于消除警告方法一般是设置成静态或弱引用。
    if (FIND_POTENTIAL_LEAKS) {
        final Class<? extends Handler> klass = getClass();
        if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                (klass.getModifiers() & Modifier.STATIC) == 0) {
            Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                klass.getCanonicalName());
        }
    }

	//mLooper是来自于sThreadLocal中ThreadLocalMap中 通过调用线程ID存储的looper,唯一
    mLooper = Looper.myLooper();
    if (mLooper == null) {
        throw new RuntimeException(
            "Can't create handler inside thread that has not called Looper.prepare()");
    }
    //mqueue来自looper， 也唯一
    mQueue = mLooper.mQueue;
    mCallback = callback;
    //标示该handler发送的数据是否为异步数据。
    mAsynchronous = async;
}
```
通过分析构造方法可验证前文提到的
handler 仅对应一个looper  MessageQueue,，翻过来不成立，也就是说会有多个handler绑定在同一个Looper中。

通过调用post(Runnable r)； postDelayed(Runnable r, long delayMillis);sendMessage(Message msg)；等方法发送的时间，最终调用下面的方法。
```
public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    return enqueueMessage(queue, msg, uptimeMillis);
}

private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    msg.target = this;
    //如果为异步，则对每一个message进行设置。
    if (mAsynchronous) {
        msg.setAsynchronous(true);
    }
    //调用enqueueMessage 进行入队Message
    return queue.enqueueMessage(msg, uptimeMillis);
}
```

还有另外一种入队方法，需要介绍：

```
public final boolean sendMessageAtFrontOfQueue(Message msg) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
            this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    //与enqueueMessage差别为uptimeMillis=0. 在messagequeue中当遇到when=0时，
    //会将该message放在对头进行处理
    return enqueueMessage(queue, msg, 0);
}
```

在需要注意下，这个remove方法，当传入null时可将MessageQueue中的所有数据remove掉。
```
public final void removeCallbacksAndMessages(Object token) {
      mQueue.removeCallbacksAndMessages(this, token);
  }
```
回过头来说一下上面的 同步分隔栏，

在Api 23 之， 通过MessageQueue 进行调用
```
/**
 * Posts a synchronization barrier to the Looper's message queue.
 *
 * Message processing occurs as usual until the message queue encounters the
 * synchronization barrier that has been posted.  When the barrier is encountered,
 * later synchronous messages in the queue are stalled (prevented from being executed)
 * until the barrier is released by calling {@link #removeSyncBarrier} and specifying
 * the token that identifies the synchronization barrier.
 *
 * This method is used to immediately postpone execution of all subsequently posted
 * synchronous messages until a condition is met that releases the barrier.
 * Asynchronous messages (see {@link Message#isAsynchronous} are exempt from the barrier
 * and continue to be processed as usual.
 *
 * This call must be always matched by a call to {@link #removeSyncBarrier} with
 * the same token to ensure that the message queue resumes normal operation.
 * Otherwise the application will probably hang!
 *
 * @return A token that uniquely identifies the barrier.  This token must be
 * passed to {@link #removeSyncBarrier} to release the barrier.
 *
 * @hide
 */
 // 该方法为hide, 正常写代码是调用不到的。
public int postSyncBarrier() {
    return postSyncBarrier(SystemClock.uptimeMillis());
}

// The next barrier token.
// Barriers are indicated by messages with a null target whose arg1 field carries the token.
// 同步分隔栏消息没有target, 并且arg1用来记录token
private int mNextBarrierToken;

private int postSyncBarrier(long when) {
    // Enqueue a new sync barrier token.
    // We don't need to wake the queue because the purpose of a barrier is to stall it.
    synchronized (this) {
        final int token = mNextBarrierToken++;
        final Message msg = Message.obtain();
        msg.markInUse();
        msg.when = when;
        msg.arg1 = token;

        Message prev = null;
        Message p = mMessages;
        if (when != 0) {
            while (p != null && p.when <= when) {
                prev = p;
                p = p.next;
            }
        }
        if (prev != null) { // invariant: p == prev.next
            msg.next = p;
            prev.next = msg;
        } else {
            msg.next = p;
            mMessages = msg;
        }
        return token;
    }
}
```
这个同步分隔message有什么作用呢？
对开发者没有明显的提供，那么就是在系统及别使用。在ViewRootImpl.java中进行了使用。

```
void scheduleTraversals() {
    if (!mTraversalScheduled) {
        mTraversalScheduled = true;
        mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();
        mChoreographer.postCallback(
                Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);
        if (!mUnbufferedInputDispatch) {
            scheduleConsumeBatchedInput();
        }
        notifyRendererOfFramePending();
        pokeDrawLockIfNeeded();
    }
}
```
为了让View能够有快速的布局和绘制，ViewRootImpl在开始measure和layout ViewTree时，会向主线程的Handler添加同步分隔message,这样后续的消息队列中的同步的消息将不会被执行，以免会影响到UI绘制，但是只有异步消息才能被执行。如果想要使用postSyncBarrier() 那么就需要使用反射进行使用。

**总结**
Looper、MessageQueue  和 Thread 一一对应。
Handler 需要绑定到一个Looper中， 一个Looper可以有多个Handler。






这是第一文章，以后会多多写的。欢迎各位指正问题！谢谢。
sy_dqs@163.com