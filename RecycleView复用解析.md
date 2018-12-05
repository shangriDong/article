
最新项目遇到一个问题，就是RecycleView的itemview会频繁拉取图片，同一时间多次拉取同一张照片。
初探，是因为该场景notifyDataSetChanged()过于频繁，一秒钟会调用5次左右，
导致ViewHolder没有复用，也不是没有复用而是复用的并没有像理想中的样式。

## **notify**
 1. notifyDataSetChanged
 2.  mObservable.notifyChanged()
 3. mObservers.get(i).onChanged();
 4. setDataSetChangedAfterLayout();
	4.1.1 markKnownViewsInvalid  
         //1 Mark all known views as invalid，仅标记可见的布局为ViewHolder.FLAG_UPDATE | ViewHolder.FLAG_INVALID
         //2 mAdapter.hasStableIds() true -> mCachedViews 标记 ViewHolder.FLAG_UPDATE | ViewHolder.FLAG_INVALID
         //3 mAdapter.hasStableIds() false -> addViewHolderToRecycledViewPool ->getRecycledViewPool().putRecycledView(holder); AND mCachedViews.clear();
         // we cannot re-use cached views in this case. Recycle them all
4.1.2 markItemDecorInsetsDirty
         //child.getLayoutParams()).mInsetsDirty = true;
         //mCachedViews layoutParams.mInsetsDirty = true;
5. requestLayout();

## 回收逻辑

1. void onLayout(boolean changed, int l, int t, int r, int b)
2. dispatchLayout()
3. void dispatchLayoutStep2()
4. void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state)
5. detachAndScrapAttachedViews(recycler); //先回收，在fill，也就是后去查找可以复用的holder。仅处理children holder
6. scrapOrRecycleView(recycler, i, v);
    6.1 viewHolder.isInvalid() && !viewHolder.isRemoved() && !mRecyclerView.mAdapter.hasStableIds() -->
        6.1.1 true recycler.recycleViewHolderInternal(viewHolder);
              addViewHolderToRecycledViewPool(holder, true); //最终放入getRecycledViewPool()
        6.1.2 false scrapView(View view)
           holder.hasAnyOfTheFlags(ViewHolder.FLAG_REMOVED | ViewHolder.FLAG_INVALID) || !holder.isUpdated() || canReuseUpdatedViewHolder(holder) -->
           6.1.2.1  true mAttachedScrap.add(holder);
           6.1.2.2  false mChangedScrap.add(holder);
总结：如果Holder无效最终放入getRecycledViewPool，否则mAttachedScrap

## 回收另外一个分支

1. onLayout
2. dispatchLayout
3. dispatchLayoutStep3
4. removeAndRecycleScrapInt //仅处理mAttachedScrap所缓存Holder
  vh.setIsRecyclable(true);
5. recycler.quickRecycleScrapView(scrap);
6. recycleViewHolderInternal(ViewHolder holder)
  `forceRecycle || holder.isRecyclable()` 成立
  `(mViewCacheMax > 0 && !holder.hasAnyOfTheFlags(ViewHolder.FLAG_INVALID | ViewHolder.FLAG_REMOVED |   ViewHolder.FLAG_UPDATE | ViewHolder.FLAG_ADAPTER_POSITION_UNKNOWN))` 不成立，包含ViewHolder.FLAG_INVALID
7. `addViewHolderToRecycledViewPool(ViewHolder holder, boolean dispatchRecycled)`
8. `getRecycledViewPool().putRecycledView(holder);`  最终放到了getRecycledViewPool中。

## 查找可复用Holder过程

1. void onLayout(boolean changed, int l, int t, int r, int b)
2. dispatchLayout()
3. void dispatchLayoutStep2()
4. void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state)
5. fill(RecyclerView.Recycler recycler, LayoutState layoutState,
            RecyclerView.State state, boolean stopOnFocusable)
6. layoutChunk(RecyclerView.Recycler recycler, RecyclerView.State state,
            LayoutState layoutState, LayoutChunkResult result)
7. LinerLayout.next
8. getViewForPosition
9. tryGetViewHolderForPositionByDeadline
  9.1 isPreLayout() --> getChangedScrapViewForPosition(int position) //如果是pre-layout，会从mChangedScrap获取复用
  9.2 getScrapOrHiddenOrCachedHolderForPosition(position, dryRun)
  		9.2.1 mAttachedScrap 首先从该处获取，
	      9.2.2 mChildHelper.findHiddenNonRemovedView(position) 拿到隐藏的ViewHolder
	      9.2.3 mCachedViews //Search in our first-level recycled view cache. 最后从一级缓存获取
	      // invalid view holders may be in cache if adapter has stable ids as they can be
	      // retrieved via getScrapOrCachedViewForId
	      // 通过原厂注释可以知道，has stable才可以使用mCachedViews缓存。在回收处也可以看出。
	      9.2.4 mAdapter.hasStableIds() --> getScrapOrCachedViewForId() //仍然需要hasStableIds
	      // 首先mAttachedScrap
	      // 之后mCachedViews
     9.3 mViewCacheExtension //外部扩展
     9.4 getRecycledViewPool().getRecycledView(type) //此处查到holder被resetInternal，即需要重新bind
     9.5 mAdapter.createViewHolder(RecyclerView.this, type); //最后，create

## bind过程

1. tryGetViewHolderForPositionByDeadline(int position,
                boolean dryRun, long deadlineNs)
2. tryBindViewHolderByDeadline
3. mAdapter.bindViewHolder(holder, offsetPosition);

至此RecyclerView的复用机制已经差不多了。至此也看到了我的问题也出现了答案的线索。
因为是`notifyDataSetChanged`出发的刷新，会将所有ViewHolder标记为FLAG_INVALID。
在回收ViewHolder时，会放入getRecycledViewPool中。
RecycledViewPool官方注释

> RecycledViewPool lets you share Views between multiple RecyclerViews.

作用为多个RecyclerViews之间复用ViewHolder。
可以通过setRecycledViewPool进行设置。
SparseArray<ScrapData> mScrap = new SparseArray<>(); 用来存储不同类型的ViewHolder

    static class ScrapData {
        ArrayList<ViewHolder> mScrapHeap = new ArrayList<>();
        int mMaxScrap = DEFAULT_MAX_SCRAP;
        long mCreateRunningAverageNs = 0;
        long mBindRunningAverageNs = 0;
    }

默认mMaxScrap大小为5个

    private static final int DEFAULT_MAX_SCRAP = 5;

也就是做多存储5个。我的项目正好有6个Item显示，就导致只能存储 1,2,3,4,5 而0没有进行存储
onBind的时候就会出现0 - 绑定 1,1 绑定2... 的问题。

解决办法为通过
new一个RecyclerView.RecycledViewPool， 并设置`recycledViewPool.setMaxRecycledViews(0, 6);`
第一个参数viewType，我这里仅有一种type，所以就写0
最终通过`mRecycleList.setRecycledViewPool(recycledViewPool);` 设置RecycledViewPool。
至此大工告成。


RecyclerView源码有1w多行。刚看的时候无从下手，向其他博主所说的见森林而不见树木。
我就是从`notifyDataSetChanged` 和 `onBindViewHolder`入手，查找上下逻辑从而追踪定位问题。
