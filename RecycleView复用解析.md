最新项目遇到一个问题，就是RecycleView的itemview会频繁拉取图片，同一时间多次拉取同一张照片。
初探，是因为该场景notifyDataSetChanged()过于频繁，一秒钟会调用5次左右，
导致ViewHolder没有复用，也不是没有复用而是复用的并没有像理想中的样式。

--notify
 1 notifyDataSetChanged
 2 mObservable.notifyChanged()
 3 mObservers.get(i).onChanged();
 4.1 setDataSetChangedAfterLayout();
   4.1.1 markKnownViewsInvalid  
         //1 Mark all known views as invalid，仅标记可见的布局为ViewHolder.FLAG_UPDATE | ViewHolder.FLAG_INVALID
         //2 mAdapter.hasStableIds() true -> mCachedViews 标记 ViewHolder.FLAG_UPDATE | ViewHolder.FLAG_INVALID

         //3 mAdapter.hasStableIds() false -> addViewHolderToRecycledViewPool ->getRecycledViewPool().putRecycledView(holder); AND mCachedViews.clear();
         // we cannot re-use cached views in this case. Recycle them all

   4.1.2 markItemDecorInsetsDirty
         //child.getLayoutParams()).mInsetsDirty = true;
         //mCachedViews layoutParams.mInsetsDirty = true;
 4.2 requestLayout();

--回收逻辑
void onLayout(boolean changed, int l, int t, int r, int b)
dispatchLayout()
void dispatchLayoutStep2()
void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state)
detachAndScrapAttachedViews(recycler); //先回收，在fill，也就是后去查找可以复用的holder。仅处理可见Holder
scrapOrRecycleView(recycler, i, v);
recycler.recycleViewHolderInternal(viewHolder);
addViewHolderToRecycledViewPool(holder, true); //最终放入getRecycledViewPool()

--回收另外一个分支
onLayout
dispatchLayout
dispatchLayoutStep3
removeAndRecycleScrapInt
  vh.setIsRecyclable(true);
recycler.quickRecycleScrapView(scrap);
recycleViewHolderInternal(ViewHolder holder)
  forceRecycle || holder.isRecyclable() 成立
  (mViewCacheMax > 0
  && !holder.hasAnyOfTheFlags(ViewHolder.FLAG_INVALID
  | ViewHolder.FLAG_REMOVED
  | ViewHolder.FLAG_UPDATE
  | ViewHolder.FLAG_ADAPTER_POSITION_UNKNOWN)) 不成立，包含ViewHolder.FLAG_INVALID
addViewHolderToRecycledViewPool(ViewHolder holder, boolean dispatchRecycled)
getRecycledViewPool().putRecycledView(holder);  最终放到了getRecycledViewPool中。

--查找可复用Holder过程
void onLayout(boolean changed, int l, int t, int r, int b)
dispatchLayout()
void dispatchLayoutStep2()
void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state)
fill(RecyclerView.Recycler recycler, LayoutState layoutState,
            RecyclerView.State state, boolean stopOnFocusable)
layoutChunk(RecyclerView.Recycler recycler, RecyclerView.State state,
            LayoutState layoutState, LayoutChunkResult result)
LinerLayout.next
getViewForPosition
tryGetViewHolderForPositionByDeadline
