最近做项目，发现一个问题，RecycleView 通过ViewHolder.getAdapterPosition() 时返回-1，看源码有几种情况会返回NO_POSITION：
1：
```
public final int getAdapterPosition() {
    if (mOwnerRecyclerView == null) {
        return NO_POSITION;
    }
    return mOwnerRecyclerView.getAdapterPositionFor(this);
}
```
mOwnerRecyclerView 即为RecyclerView.this本身。
2:
```
int getAdapterPositionFor(ViewHolder viewHolder) {
    if (viewHolder.hasAnyOfTheFlags( ViewHolder.FLAG_INVALID |
            ViewHolder.FLAG_REMOVED | ViewHolder.FLAG_ADAPTER_POSITION_UNKNOWN)
            || !viewHolder.isBound()) {
        return RecyclerView.NO_POSITION;
    }
    return mAdapterHelper.applyPendingUpdatesToPosition(viewHolder.mPosition);
}
```
当ViewHolder处于FLAG_REMOVED 等状态时会返回NO_POSITION。

为什么会有NO_POSITION看一下官方解释：

> Returns the Adapter position of the item represented by this ViewHolder.

> Note that this might be different than the getLayoutPosition() if there are pending adapter updates but a new layout pass has not happened yet.

>RecyclerView does not handle any adapter updates until the next layout traversal. This may create temporary inconsistencies between what user sees on the screen and what adapter contents have. This inconsistency is not important since it will be less than 16ms but it might be a problem if you want to use ViewHolder position to access the adapter. Sometimes, you may need to get the exact adapter position to do some actions in response to user events. In that case, you should use this method which will calculate the Adapter position of the ViewHolder.

> Note that if you've called notifyDataSetChanged(), until the next layout pass, the return value of this method will be NO_POSITION.

> The adapter position of the item if it still exists in the adapter. NO_POSITION if item has been removed from the adapter, notifyDataSetChanged() has been called after the last layout pass or the ViewHolder has already been recycled.

正常情况是不会出现，我的操作是，删除、移动一个item，之后notifyItemMoved() 再次获取getAdapterPosition()就会返回NO_POSITION

我的解决方案是，在ViewHolder中持有数据源Bean。 通过bean在list中的位置来获取position。

参考链接：
http://blog.csdn.net/newmandirl/article/details/50854733
https://stackoverflow.com/questions/36908448/android-getadapterposition-returns-1-after-deleting-an-item
https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html#getAdapterPosition%28%29