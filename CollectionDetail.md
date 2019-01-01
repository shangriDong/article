**HashMap**
1. hash计算

```return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16); ```

取key的hashCode并低16与高16异或运算

 >这样设计保证了对象的hashCode的高16位的变化能反应到低16位中，相比较而言减少了过多的位运算，是一种折中的设计。

2. NaN

Not-a-Number，在实际数值计算的过程中可能会出现一些非法的操作，导致产生了非法的数值，比如说除零，通过NaN来表示这样一个非数值，NaN与任何浮点数（包括自身）的比较结果都为假，即 (NaN ≠ x) = false，这是NaN独有的特性，所以可以使用与自己相比来确定当前数值是不是一个正常的数。

3. put

  1. ```n = (tab = resize()).length``` 找到table调整后的大小

  2. ```(p = tab[i = (n - 1) & hash]) == null``` 如果通过线性探测方法找到tab[index]，如果该位置为null，直接构造结点放到该位置。否则3.

  3. ```p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k)))``` 如果通过tab[index]找到的元素与要插入的元素是同一个，则在该位置修改

  4. ```p instanceof TreeNode``` 如果 上面不成立，但该tab[index]的元素为TreeNode，则通过putTreeVal进行遍历插入

  5. 最后，如果该位置为一个单向列表，则通过单向列表的方式遍历插入。如果该单向列表长度超过8，则通过treeifyBin进行树化。
  6. 如果该key找到了Node，则通过```!onlyIfAbsent || oldValue == null``` 判断是否替换新value
  7. 如果没有通过key找到Node，即为新插入元素，则进行
  ```
  ++modCount; //编辑次数++
  if (++size > threshold) resize(); //查过该值则进行大小调整
  afterNodeInsertion(evict);```

4. ```size > threshold || (tab = table) == null || (n = tab.length) == 0``` 则进行resize()

**TreeMap**
1. 通过红黑树实现了有序的NavigableMap，通过Comparator或者插入元素的Comparator进行比较排序。小的在left，大的在right

```
final TreeMapEntry<K,V> getEntry(Object key) {
    // Offload comparator-based version for sake of performance
    if (comparator != null)
        return getEntryUsingComparator(key); //设置比较器
    if (key == null)
        throw new NullPointerException();
    @SuppressWarnings("unchecked")
        Comparable<? super K> k = (Comparable<? super K>) key;
    TreeMapEntry<K,V> p = root;
    while (p != null) {
        int cmp = k.compareTo(p.key);
        if (cmp < 0)
            p = p.left;
        else if (cmp > 0)
            p = p.right;
        else
            return p;
    }
    return null;
}
```
