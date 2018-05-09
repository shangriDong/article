还是先来看一下官方介绍：
```
/**
 * Doubly-linked list implementation of the {@code List} and {@code Deque}
 * interfaces.  Implements all optional list operations, and permits all
 * elements (including {@code null}).
 *
 * <p>All of the operations perform as could be expected for a doubly-linked
 * list.  Operations that index into the list will traverse the list from
 * the beginning or the end, whichever is closer to the specified index.
 *
 * <p><strong>Note that this implementation is not synchronized.</strong>
 * If multiple threads access a linked list concurrently, and at least
 * one of the threads modifies the list structurally, it <i>must</i> be
 * synchronized externally.  (A structural modification is any operation
 * that adds or deletes one or more elements; merely setting the value of
 * an element is not a structural modification.)  This is typically
 * accomplished by synchronizing on some object that naturally
 * encapsulates the list.
 *
 * If no such object exists, the list should be "wrapped" using the
 * {@link Collections#synchronizedList Collections.synchronizedList}
 * method.  This is best done at creation time, to prevent accidental
 * unsynchronized access to the list:<pre>
 *   List list = Collections.synchronizedList(new LinkedList(...));</pre>
 *
 * <p>The iterators returned by this class's {@code iterator} and
 * {@code listIterator} methods are <i>fail-fast</i>: if the list is
 * structurally modified at any time after the iterator is created, in
 * any way except through the Iterator's own {@code remove} or
 * {@code add} methods, the iterator will throw a {@link
 * ConcurrentModificationException}.  Thus, in the face of concurrent
 * modification, the iterator fails quickly and cleanly, rather than
 * risking arbitrary, non-deterministic behavior at an undetermined
 * time in the future.
 *
 * <p>Note that the fail-fast behavior of an iterator cannot be guaranteed
 * as it is, generally speaking, impossible to make any hard guarantees in the
 * presence of unsynchronized concurrent modification.  Fail-fast iterators
 * throw {@code ConcurrentModificationException} on a best-effort basis.
 * Therefore, it would be wrong to write a program that depended on this
 * exception for its correctness:   <i>the fail-fast behavior of iterators
 * should be used only to detect bugs.</i>
 *
 * <p>This class is a member of the
 * <a href="{@docRoot}openjdk-redirect.html?v=8&path=/technotes/guides/collections/index.html">
 * Java Collections Framework</a>.
 *
 * @author  Josh Bloch
 * @see     List
 * @see     ArrayList
 * @since 1.2
 * @param <E> the type of elements held in this collection
 */
 // 双链表实现了List、Deque接口。实现了所有可选的list操作，并且允许操作所有元素，元素可以为null。
 // 对于双链表，所有操作都可以执行。索引到列表的操作将从头或者从尾部遍历，两者是选择离指定索引最近的一种方式。
 // 注意的是，该实现为非同步的。如果多线程并发处使用列表，并且至少有一个线程修改了list的结构，那么就
 // 需要使用外部同步方式。结构修改包括add、delete一个或多个元素；仅仅设置元素的值并不是结构性修改。
 // 通常需要Object来做同步，从而自然的封装list。
 // 如果不类似的object存在，那么list必须使用Collections#synchronizedList Collections.synchronizedList
 // 进行封装。最好在创建的时候完成，以防止意外的非同步方式获取list。方法为：
 // List list = Collections.synchronizedList(new LinkedList(...));

 // 通过class的iterator和listIterator方法返回的iterators为快速失效的，就是说，如果list
 // 在创建iterator之后的任意时间内发生结构性修改，除非通过Iterator自己的remove或者add方法进行修改，
 // 那么iterator就发抛出ConcurrentModificationException。因此，面对并发修改的情况下，
 // interator会迅速并彻底的失效，而不是冒险的，在未来不确定的时间内发生非确定性操作。

 // 注意，iterator的快速失效行为并不能保证，也就是在非同步并发修改，不能做出任何强硬的保证。
 // Fail-fast iterators会尽力的剖出ConcurrentModificationException。
 // 因此，如果编写的程序依赖于这个异常去保证正确性是错误的，迭代器的快速失效性仅用来检测bug。
```
