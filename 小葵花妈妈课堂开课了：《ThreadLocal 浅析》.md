**ThreadLocal**
先看一下一下官方的解释：

> /**
 * This class provides thread-local variables.  These variables differ from
 * their normal counterparts in that each thread that accesses one (via its
 * <tt>get</tt> or <tt>set</tt> method) has its own, independently initialized
 * copy of the variable.  <tt>ThreadLocal</tt> instances are typically private
 * static fields in classes that wish to associate state with a thread (e.g.,
 * a user ID or Transaction ID).
 **/
 
我自己翻译了一下：
ThreadLocal类提供了线程本地局部变量，这些局部变量区别于其他变量是因为，每一个线程都会有它自己的值，这个变量会在每一个线程中独立进行初始化。ThreadLocal实例通常都是私有静态的，这样做是希望将状态与线程联系起来。
> 
> /** Each thread holds an implicit reference to its copy of a thread-local
 * variable as long as the thread is alive and the <tt>ThreadLocal</tt>
 * instance is accessible; after a thread goes away, all of its copies of
 * thread-local instances are subject to garbage collection (unless other
 * references to these copies exist).
 **/
每一个线程都隐士的引用着他自己的线程本地变量的副本，只要线程一直存活并且ThreadLocal实例可以访问。线程消失之后，所有的它自己的本地线程实例靠背对象都将会被回收（除非还有其他引用指向着这些副本）。

按照官方这两段说明，大概意思是说ThreadLocal提供了一种能力：一个变量在多线程中都具有相同的初值，并且都存在于各自线程中彼此独立互不影像、并且只要在同一线程中访问都是同一个变量。当线程结束后，该变量也会被回收。


下面介绍一下ThreadLocal源码：

```
/**
* ThreadLocals rely on per-thread linear-probe hash maps attached
* to each thread (Thread.threadLocals and
* inheritableThreadLocals).  The ThreadLocal objects act as keys,
* searched via threadLocalHashCode.  This is a custom hash code
* (useful only within ThreadLocalMaps) that eliminates collisions
* in the common case where consecutively constructed ThreadLocals
* are used by the same threads, while remaining well-behaved in
* less common cases.
*/
/**
* ThreadLocal依靠线性探测哈希表附着到每个Thread.
* ThreadLocal对象都使用threadLocalHashCode来进行区分。
* 这是一个自定义的哈希值（仅用在ThreadLocalMaps内部），用来区分在
* 同一个线程中连续构造的多个ThreadLocal对象。 
*/
/**
* threadLocalHashCode 在set、get、remove方法中都是将这个值当做key进行查找
*/
private final int threadLocalHashCode = nextHashCode();

public ThreadLocal();       
protected T initialValue();
T get();
void set(T value);
void remove();
```

下面介绍一下简单示例用法：

```
public class ThreadLocalTestActivity extends FragmentActivityRoot {
    private static final ThreadLocalTest threadLocalTest = new ThreadLocalTest();

    public static TestBean getTestBean() {
        TestBean testBean = threadLocalTest.get();
        if (testBean == null) {
            testBean = new TestBean();
            threadLocalTest.set(testBean);
        }
        return testBean;
    }

    private static class ThreadLocalTestRunable implements Runnable {
        @Override
        public void run() {
            int count = 0;

            while(count < 10) {
                getTestBean().testInt += 100;
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                System.out.printf(Thread.currentThread().getName()
                        + ": threadLocal=%d\n", getTestBean().testInt);
                count++;
            }
        }
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_test_thread_local);
        for( int i = 0;i < 2;i++) {
            Thread thread = new Thread(new ThreadLocalTestRunable());
            thread.start();
        }
    }
}
```
日志如下：

> d: fd=88
03-26 20:52:34.354 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=101
03-26 20:52:34.354 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=101
03-26 20:52:34.455 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=201
03-26 20:52:34.455 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=201
03-26 20:52:34.556 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=301
03-26 20:52:34.556 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=301
03-26 20:52:34.656 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=401
03-26 20:52:34.656 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=401
03-26 20:52:34.757 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=501
03-26 20:52:34.757 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=501
03-26 20:52:34.857 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=601
03-26 20:52:34.857 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=601
03-26 20:52:34.957 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=701
03-26 20:52:34.957 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=701
03-26 20:52:35.059 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=801
03-26 20:52:35.060 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=801
03-26 20:52:35.162 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=901
03-26 20:52:35.165 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=901
03-26 20:52:35.264 8163-8592/com.dqs.shangri.testinstrumentation I/System.out: Thread-5: threadLocal=1001
03-26 20:52:35.266 8163-8593/com.dqs.shangri.testinstrumentation I/System.out: Thread-6: threadLocal=1001

可以看到每个线程都有自己的TestBean 对象。每个线程在调用getTestBean()时都是返回的同一个对象。
为什么会有如此特性呢，就要分析get和set源码。

先来介绍一些T get()方法：

```
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    //第一次调用时，map为null
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null)
            return (T)e.value;
    }
    //1 设置初始值
    return setInitialValue();
}

/**
 * Variant of set() to establish initialValue. Used instead
 * of set() in case user has overridden the set() method.
 *
 * @return the initial value
 */
private T setInitialValue() {
    T value = initialValue();
    Thread t = Thread.currentThread();
    //2 getMap第一次拿到的仍为null
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else {
	    //3 创建map
        createMap(t, value);
    }
    return value;
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

//可复写该方法。
protected T initialValue() {
    return null;
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;
}

void createMap(Thread t, T firstValue) {
	//4 到这就明朗了，第一次使用的时候，会给每个线程的threadLocals进行初始化，
	//类型为ThreadLocal.ThreadLocalMap
	//一次调用的firstValue来自T initialValue()；
	//如果没有复写该方法则为null
    t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```
上面的第一次get调用路径已经明了，
首先要拿到当前线程Thread，如果当前的线程的threadLocals为null，就创建一个。
其中创建的ThreadLocalMap的官方说法为：

> /**
         * Construct a new map initially containing (firstKey, firstValue).
         * ThreadLocalMaps are constructed lazily, so we only create
         * one when we have at least one entry to put in it.
         */
   构造一个map,使用初始值（firstKey, firstValue）。ThreadLocalMaps 是懒加载的，
   当我们即将要将entry 压入到Map中时才进行创建。

也就是说ThreadLocalMap是一种map，通过key-value形式存储我们给他的值。
也就是说每一个线程都有自己的ThreadLocalMap，key为ThreadLocal，value为任意Object。

现在先解释一下**ThreadLocalMap**

> /**
     * ThreadLocalMap is a customized hash map suitable only for
     * maintaining thread local values. No operations are exported
     * outside of the ThreadLocal class. The class is package private to
     * allow declaration of fields in class Thread.  To help deal with
     * very large and long-lived usages, the hash table entries use
     * WeakReferences for keys. However, since reference queues are not
     * used, stale entries are guaranteed to be removed only when
     * the table starts running out of space.
     */
     ThreadLocalMap 是一个定制的哈希map，仅适合用来维护线程本地值。
     ThreadLocal 类外不能操作ThreadLocalMap 。
     该类仅能在Thread class中使用。
     用来解决非常大和长时间的引用，这些引用在哈希表内使用弱引用方式存储。
     然而，自从引用不被使用时，无用的条目会被删除仅当表的空间将被耗尽的时候。

```
/**
 * The entries in this hash map extend WeakReference, using
 * its main ref field as the key (which is always a
 * ThreadLocal object).  Note that null keys (i.e. entry.get()
 * == null) mean that the key is no longer referenced, so the
 * entry can be expunged from table.  Such entries are referred to
 * as "stale entries" in the code that follows.
 */
 /**
 * 在当前哈希Map中的条目信息是继承自弱引用，使用他的主引用作为key（在
 * 当前类中即为ThredLocal对象）。当keys为null的时候意味着不在被引用，
 * 所以这条就可以删除。在之后的代码中这类条目就称为“stale entries”。
 */
static class Entry extends WeakReference<ThreadLocal> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal k, Object v) {
        super(k);
        value = v;
    }
}

//下面介绍ThreadLocalMap的成员变量
/**
 * The initial capacity -- MUST be a power of two.
 * 初始容量，一定必须是2的幂
 */
private static final int INITIAL_CAPACITY = 16;

/**
 * The table, resized as necessary.
 * table.length MUST always be a power of two.
 * table为存储Entry的表，在必要的时候可以调整大小。
 * 表长一定为2的幂
 */
private Entry[] table;

/**
 * The number of entries in the table.
 * table表中存储元素数量
 */
private int size = 0;

/**
 * The next size value at which to resize.
 * 
 * 当达到threshold值的时候，进行resize
 */
private int threshold; // Default to 0

//介绍一下 构造方法
/**
 * Construct a new map initially containing (firstKey, firstValue).
 * ThreadLocalMaps are constructed lazily, so we only create
 * one when we have at least one entry to put in it.
 */
ThreadLocalMap(ThreadLocal firstKey, Object firstValue) {
    //table 为存储Enrty的表结构，数组形式。容量必须为2的幂数。默认初始值为16
    table = new Entry[INITIAL_CAPACITY];
    //每一个ThreadLocal都有一个唯一的id 即threadLocalHashCode 。
    //通过与15做取余操作，找到当前ThreadLocal在table中的位置。
    int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
    table[i] = new Entry(firstKey, firstValue);
    //更新table当前容量
    size = 1;
    //设置下次resize的目标值
    setThreshold(INITIAL_CAPACITY);
}

/**
 * Construct a new map including all Inheritable ThreadLocals
 * from given parent map. Called only by createInheritedMap.
 *
 * 提供了一个可以初始table的构造方法，仅在createInheritedMap使用。
 * @param parentMap the map associated with parent thread.
 */
private ThreadLocalMap(ThreadLocalMap parentMap) {
    Entry[] parentTable = parentMap.table;
    int len = parentTable.length;
    setThreshold(len);
    table = new Entry[len];

	//方法中介绍了，当散列表map中遇到冲突时的解决办法，
	//该处使用的是开放定址法。后文会介绍该方法。
    for (int j = 0; j < len; j++) {
        Entry e = parentTable[j];
        if (e != null) {
            ThreadLocal key = e.get();
            if (key != null) {
                Object value = key.childValue(e.value);
                Entry c = new Entry(key, value);
                int h = key.threadLocalHashCode & (len - 1);
                //因为使用开放定址法，所以h会一直向后寻找。所以table为数组形式。
                while (table[h] != null)
                    h = nextIndex(h, len);
                table[h] = c;
                size++;
            }
        }
    }
}

//下面介绍ThreadLocalMap的get方法
/**
 * Get the entry associated with key.  This method
 * itself handles only the fast path: a direct hit of existing
 * key. It otherwise relays to getEntryAfterMiss.  This is
 * designed to maximize performance for direct hits, in part
 * by making this method readily inlinable.
 *
 * 通过key可以获得相关的enrty。方法本身只处理快速路径：直接通过存在的key进行寻找。
 * 如果找不到则会跳转到getEntryAfterMiss中。这种办法是为了能以最优的性能找到目标，
 * 因为大部分是可以通过改法直接找到。
 * 
 * @param  key the thread local object
 * @return the entry associated with key, or null if no such
 */
private Entry getEntry(ThreadLocal key) {
	//首先通过key.threadLocalHashCode 和表长取余，找到位置
    int i = key.threadLocalHashCode & (table.length - 1);
    Entry e = table[i];
    if (e != null && e.get() == key)
        return e;
    else {
        //如果没有找到e,或者key不相等，则通过getEntryAfterMiss查找
        return getEntryAfterMiss(key, i, e);
    }
}

/**
 * Version of getEntry method for use when key is not found in
 * its direct hash slot.
 *  如果在直接散列表中没有通过key找到，将调用该方法。
 *
 * @param  key the thread local object
 * @param  i the table index for key's hash code
 * @param  e the entry at table[i]
 * @return the entry associated with key, or null if no such
 */
private Entry getEntryAfterMiss(ThreadLocal key, int i, Entry e) {
    Entry[] tab = table;
    int len = tab.length;

    while (e != null) {
        ThreadLocal k = e.get();
        if (k == key)
            return e;
        if (k == null) {
            // 如果找到的entry的key被收回，则进行回收entry操作。
            expungeStaleEntry(i);
        } else {
            //因为使用开放地址法，直接向后寻找
            i = nextIndex(i, len);
        }
        e = tab[i];
        //直到找到空位置时，return null;
    }
    return null;
}

/**
 * Expunge a stale entry by rehashing any possibly colliding entries
 * lying between staleSlot and the next null slot.  This also expunges
 * any other stale entries encountered before the trailing null.  See
 * Knuth, Section 6.4
 * 
 * 在遇到冲突操作时，都会进行移除无用entry操作。移除范围为当前位置到下一个null直接。
 * 
 * 
 * @param staleSlot index of slot known to have null key
 * @return the index of the next null slot after staleSlot
 * (all between staleSlot and this slot will have been checked
 * for expunging).
 */
private int expungeStaleEntry(int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;

    // expunge entry at staleSlot
    tab[staleSlot].value = null;
    tab[staleSlot] = null;
    size--;

    // Rehash until we encounter null
    Entry e;
    int i;
    for (i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        ThreadLocal k = e.get();
        if (k == null) {
            e.value = null;
            tab[i] = null;
            size--;
        } else {
            int h = k.threadLocalHashCode & (len - 1);
            if (h != i) {
                //当删除无用元素后，会对table中元素重新移动位置。以确保下次冲突查询时，
                //能够找到
                tab[i] = null;

                // Unlike Knuth 6.4 Algorithm R, we must scan until
                // null because multiple entries could have been stale.
                while (tab[h] != null)
                    h = nextIndex(h, len);
                tab[h] = e;
            }
        }
    }
    return i;
}

//下面介绍set entry方法
/**
 * Set the value associated with key.
 *
 * @param key the thread local object
 * @param value the value to be set
 */
private void set(ThreadLocal key, Object value) {

    // We don't use a fast path as with get() because it is at
    // least as common to use set() to create new entries as
    // it is to replace existing ones, in which case, a fast
    // path would fail more often than not.

	//我们不要使用快速路径去调用get方法，因为他至少在通常情况下使用
	//set方法是创建一个新的entry用来替换先用的，在这种情况下，通常就会失败。
    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len-1);

    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        ThreadLocal k = e.get();

        if (k == key) {
            //如果当前entry的key相同，直接替换value
            e.value = value;
            return;
        }

        if (k == null) {
            //如果当前位置的entry为staleEntry，则进行替换
            replaceStaleEntry(key, value, i);
            return;
        }
    }

    //如果没有遇到staleEntry，则进行创建entry
    tab[i] = new Entry(key, value);
    int sz = ++size;
    //尝试删除无用对象，如果没有删除并且当前table数量已经大于等于threshold 
    //将尝试进行resize
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
        rehash();
}

/**
 * Heuristically scan some cells looking for stale entries.
 * This is invoked when either a new element is added, or
 * another stale one has been expunged. It performs a
 * logarithmic number of scans, as a balance between no
 * scanning (fast but retains garbage) and a number of scans
 * proportional to number of elements, that would find all
 * garbage but would cause some insertions to take O(n) time.
 *
 * @param i a position known NOT to hold a stale entry. The
 * scan starts at the element after i.
 *
 * @param n scan control: <tt>log2(n)</tt> cells are scanned,
 * unless a stale entry is found, in which case
 * <tt>log2(table.length)-1</tt> additional cells are scanned.
 * When called from insertions, this parameter is the number
 * of elements, but when from replaceStaleEntry, it is the
 * table length. (Note: all this could be changed to be either
 * more or less aggressive by weighting n instead of just
 * using straight log n. But this version is simple, fast, and
 * seems to work well.)
 *
 * 当添加新元素或者有其他的无用元素被删除时会调用该方法启发性的进行删除无用元素。
 * 将以对数形式进行探索，目的为了到达一种平衡介于不进行探索和多次扫描之间。
 * 这样做可以找到所有清除对象并且只使用O(n)的时间。
 * 
 * @return true if any stale entries have been removed.
 */
private boolean cleanSomeSlots(int i, int n) {
    boolean removed = false;
    Entry[] tab = table;
    int len = tab.length;
    do {
        i = nextIndex(i, len);
        Entry e = tab[i];
        if (e != null && e.get() == null) {
            n = len;
            removed = true;
            //找到无用entry,即进行删除
            i = expungeStaleEntry(i);
        }
        //n无符号右移
    } while ( (n >>>= 1) != 0);
    //如果删除了返回true
    return removed;
}

/**
* Re-pack and/or re-size the table. First scan the entire
* table removing stale entries. If this doesn't sufficiently
* shrink the size of the table, double the table size.
*/
private void rehash() {
	//遍历整个Table，删除所有无用对象。
   expungeStaleEntries();

   // Use lower threshold for doubling to avoid hysteresis
   // 如果size大于等于3/4 threshold，即进行resize
   if (size >= threshold - threshold / 4)
       resize();
}

/**
 * Expunge all stale entries in the table.
 * 删除所有无用对象
 */
private void expungeStaleEntries() {
    Entry[] tab = table;
    int len = tab.length;
    for (int j = 0; j < len; j++) {
        Entry e = tab[j];
        if (e != null && e.get() == null)
            expungeStaleEntry(j);
    }
}

/**
 * Double the capacity of the table.
 * double表的容量
 */
private void resize() {
    Entry[] oldTab = table;
    int oldLen = oldTab.length;
    int newLen = oldLen * 2;
    Entry[] newTab = new Entry[newLen];
    int count = 0;

	//复制表的内容
    for (int j = 0; j < oldLen; ++j) {
        Entry e = oldTab[j];
        if (e != null) {
            ThreadLocal k = e.get();
            if (k == null) {
                e.value = null; // Help the GC
            } else {
                int h = k.threadLocalHashCode & (newLen - 1);
                while (newTab[h] != null)
                    h = nextIndex(h, newLen);
                newTab[h] = e;
                count++;
            }
        }
    }

    setThreshold(newLen);
    size = count;
    table = newTab;
}

/**
 * Replace a stale entry encountered during a set operation
 * with an entry for the specified key.  The value passed in
 * the value parameter is stored in the entry, whether or not
 * an entry already exists for the specified key.
 * 
 * 在进行set操作时，通过key找到的插入位置为无效条目时，进行替换。
 * 无轮key所指向的条目是否存在，都会存储通过参数传进来的value。
 * 
 * As a side effect, this method expunges all stale entries in the
 * "run" containing the stale entry.  (A run is a sequence of entries
 * between two null slots.)
 * 
 * 该方法有一个副作用，方法会删除在run中的所有无效条目。
 * run的意思是指在两个空槽之间的所有条目。
 * 
 * @param  key the key
 * @param  value the value to be associated with key
 * @param  staleSlot index of the first stale entry encountered while
 *         searching for key.
 */
private void replaceStaleEntry(ThreadLocal key, Object value,
                               int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;
    Entry e;

    // Back up to check for prior stale entry in current run.
    // We clean out whole runs at a time to avoid continual
    // incremental rehashing due to garbage collector freeing
    // up refs in bunches (i.e., whenever the collector runs).
    int slotToExpunge = staleSlot;
    //找到距离当前插入位置最近的null条目之间的，
    //最远端的 无效条目slotToExpunge 
    for (int i = prevIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = prevIndex(i, len))
        if (e.get() == null)
            slotToExpunge = i;

    // Find either the key or trailing null slot of run, whichever
    // occurs first
    for (int i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        ThreadLocal k = e.get();

        // If we find key, then we need to swap it
        // with the stale entry to maintain hash table order.
        // The newly stale slot, or any other stale slot
        // encountered above it, can then be sent to expungeStaleEntry
        // to remove or rehash all of the other entries in run.
        if (k == key) {
            e.value = value;

            tab[i] = tab[staleSlot];
            tab[staleSlot] = e;

            // Start expunge at preceding stale entry if it exists
            if (slotToExpunge == staleSlot)
                slotToExpunge = i;
            cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
            return;
        }

        // If we didn't find stale entry on backward scan, the
        // first stale entry seen while scanning for key is the
        // first still present in the run.
        if (k == null && slotToExpunge == staleSlot)
            slotToExpunge = i;
    }

    // If key not found, put new entry in stale slot
    tab[staleSlot].value = null;
    tab[staleSlot] = new Entry(key, value);

    // If there are any other stale entries in run, expunge them
    if (slotToExpunge != staleSlot)
        cleanSomeSlots(expungeStaleEntry(slotToExpunge), len);
}
```
散列表 开放定址法 具体介绍请跳转此处
[散列表解决冲突的办法](https://blog.csdn.net/dongqiushan/article/details/79719437)


那么回头来说get方法：
```
public T get() {
    Thread t = Thread.currentThread();
    //再次调用时 map已经创建成功
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        //根据map的getEntry可知，如果不遇到冲突，会很快找到元素，
        //如果遇到冲突会使用开放定址法来找到元素
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null)
            return (T)e.value;
    }
    return setInitialValue();
}

/**
 * Remove the entry for key.
 * 删除指定key找到的value
 */
private void remove(ThreadLocal key) {
    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len-1);
    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
        if (e.get() == key) {
            e.clear();
            expungeStaleEntry(i);
            return;
        }
    }
}
```
当我们setEntry之后，如果遇到不再使用要主动remove。这样可以达到性能最优。

总结一下：

**ThreadLocal使用在多线程中，并且想要每一个线程中彼此独立。那么使用ThreadLocal就是合适的。**

ThreadLocal内部使用数组形式的哈希表，通过开放定址法解决冲突问题。

ThreadLocal 通常要定义成为：
```
private static ThreadLocal threadLocal;
```
可调用方法：
```
protected T initialValue();   //可以重写该方法，赋值初始值
T get();  //使用该方法获取值
void set(T value); //设置value
void remove(); //不需要的时候remove
```

sy_dqs@163.com