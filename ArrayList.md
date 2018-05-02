官方文档源码介绍：

```
/**
 * Resizable-array implementation of the <tt>List</tt> interface.  Implements
 * all optional list operations, and permits all elements, including
 * <tt>null</tt>.  In addition to implementing the <tt>List</tt> interface,
 * this class provides methods to manipulate the size of the array that is
 * used internally to store the list.  (This class is roughly equivalent to
 * <tt>Vector</tt>, except that it is unsynchronized.)
 *
 * <p>The <tt>size</tt>, <tt>isEmpty</tt>, <tt>get</tt>, <tt>set</tt>,
 * <tt>iterator</tt>, and <tt>listIterator</tt> operations run in constant
 * time.  The <tt>add</tt> operation runs in <i>amortized constant time</i>,
 * that is, adding n elements requires O(n) time.  All of the other operations
 * run in linear time (roughly speaking).  The constant factor is low compared
 * to that for the <tt>LinkedList</tt> implementation.
 *
 * <p>Each <tt>ArrayList</tt> instance has a <i>capacity</i>.  The capacity is
 * the size of the array used to store the elements in the list.  It is always
 * at least as large as the list size.  As elements are added to an ArrayList,
 * its capacity grows automatically.  The details of the growth policy are not
 * specified beyond the fact that adding an element has constant amortized
 * time cost.
 *
 * <p>An application can increase the capacity of an <tt>ArrayList</tt> instance
 * before adding a large number of elements using the <tt>ensureCapacity</tt>
 * operation.  This may reduce the amount of incremental reallocation.
 *
 * <p><strong>Note that this implementation is not synchronized.</strong>
 * If multiple threads access an <tt>ArrayList</tt> instance concurrently,
 * and at least one of the threads modifies the list structurally, it
 * <i>must</i> be synchronized externally.  (A structural modification is
 * any operation that adds or deletes one or more elements, or explicitly
 * resizes the backing array; merely setting the value of an element is not
 * a structural modification.)  This is typically accomplished by
 * synchronizing on some object that naturally encapsulates the list.
 *
 * If no such object exists, the list should be "wrapped" using the
 * {@link Collections#synchronizedList Collections.synchronizedList}
 * method.  This is best done at creation time, to prevent accidental
 * unsynchronized access to the list:<pre>
 *   List list = Collections.synchronizedList(new ArrayList(...));</pre>
 *
 * <p><a name="fail-fast">
 * The iterators returned by this class's {@link #iterator() iterator} and
 * {@link #listIterator(int) listIterator} methods are <em>fail-fast</em>:</a>
 * if the list is structurally modified at any time after the iterator is
 * created, in any way except through the iterator's own
 * {@link ListIterator#remove() remove} or
 * {@link ListIterator#add(Object) add} methods, the iterator will throw a
 * {@link ConcurrentModificationException}.  Thus, in the face of
 * concurrent modification, the iterator fails quickly and cleanly, rather
 * than risking arbitrary, non-deterministic behavior at an undetermined
 * time in the future.
 *
 * <p>Note that the fail-fast behavior of an iterator cannot be guaranteed
 * as it is, generally speaking, impossible to make any hard guarantees in the
 * presence of unsynchronized concurrent modification.  Fail-fast iterators
 * throw {@code ConcurrentModificationException} on a best-effort basis.
 * Therefore, it would be wrong to write a program that depended on this
 * exception for its correctness:  <i>the fail-fast behavior of iterators
 * should be used only to detect bugs.</i>
 *
 * <p>This class is a member of the
 * <a href="{@docRoot}openjdk-redirect.html?v=8&path=/technotes/guides/collections/index.html">
 * Java Collections Framework</a>.
 *
 * @author  Josh Bloch
 * @author  Neal Gafter
 * @see     Collection
 * @see     List
 * @see     LinkedList
 * @see     Vector
 * @since   1.2
 */
/**
 * Resizable-array实现了List的接口。实现所有可选的列表操作， 并且允许所有元素为null.
 * 不仅实现了List接口，这个类也提供了操作数组的大小的方法，这些方法通常用来存储list.
 * 除了该类是不同步的，那么这个类就相当于Vector。
 * 
 * ---
 * 每一个ArrayList实例都有容量。容量是list中存储元素的数组的个数。它总是至少和list的大小一样大。
 * 作为一个元素添加到ArrayList中，容量自动增长。增长策略的细节并不是在添加元素具有固定摊销时间
 * 成本的情况下指定的。
 * 在使用ensureCapacity方法添加大量元素之前，应用程序可以改变ArrayList实例的容量。
 * 这样可以减少增量再分配的数量。
 * 需要注意的是，这个实现并不是同步的。如果多线程同时访问同一个ArrayList实例，并且至少有一个
 * 线程修改了list的结构，他必须与外部同步。结构上的修改包括add、delete一个或多个元素，或者调整
 * 数组的大小；只是设置一个元素的具体值并不是一种结构调整。通常需要一些object做同步，
 * 来完成自然分装列表。
 * 如果没有object去进行同步的话，那么list就需要使用
 * Collections.synchronizedList Collections.synchronizedList方法进行分装。
 * 这样可以防止意外的并且没有进行同步的情况下去或得list实例。
 * List list = Collections.synchronizedList(new ArrayList(...));
 * 可以通过类的iterator()方法获得迭代器，并且listIterator()方法是快速失效，如果list的结构在iterator
 * 创建之后的任意时间内被修改了，如果通过迭代器自身 ListIterator#remove()、ListIterator#add(Object)
 * 方法外iterator会抛出一个异常ConcurrentModificationException。因此面对并行修改，迭代器会快速并且干净
 * 返回失败，而不是在未来某个不确定的时间内冒险或者不确定的行为。
 * 注意，迭代器的fail-fast行为不能得到保证，因为，在没有同步并发修改的情况下不会获得任何的硬保证。
 * fail-fast迭代器会抛出ConcurrentModificationException异常。因此写程序时依赖这个异常来保证
 * 正确性是错误的，迭代器的fail-fast行为应该只能用来检测缺陷。
 */
```
**未完待续**