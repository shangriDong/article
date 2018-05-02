如题，基于android PullToRefresh的支持上拉刷新下拉加载的RecycleView
直接上码：

```
public class PullToRefreshRecycleView extends PullToRefreshBase<RecyclerView> {

    private OnHeaderRefreshListener<RecyclerView> m_OnHeaderRefreshListener;
    private OnFooterRefreshListener<RecyclerView> m_OnFooterRefreshListener;

    private final OnRefreshListener2<RecyclerView> m_OnRefreshListener = new OnRefreshListener2<RecyclerView>() {

        @Override
        public void onPullDownToRefresh(PullToRefreshBase<RecyclerView> view) {
            if (m_OnHeaderRefreshListener != null) {
                m_OnHeaderRefreshListener.onHeaderRefresh(view);
            }
        }

        @Override
        public void onPullUpToRefresh(PullToRefreshBase<RecyclerView> view) {
            if (m_OnFooterRefreshListener != null) {
                m_OnFooterRefreshListener.onFooterRefresh(view);
            }
        }

    };

    @Override
    protected void onRefreshing(boolean doScroll) {
        super.onRefreshing(doScroll);
        if(Mode.PULL_FROM_END == getCurrentMode()){
            getFooterLayout().refreshing();
        }else if(Mode.PULL_FROM_START == getCurrentMode()){
            getHeaderLayout().refreshing();
        }
    }

    @Override
    protected void onReleaseToRefresh() {
        super.onReleaseToRefresh();
        inLoad = false;
    }

    private RecyclerView view;

    public PullToRefreshRecycleView(Context context) {
        super(context);
        init();
    }

    public PullToRefreshRecycleView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public PullToRefreshRecycleView(Context context, Mode mode) {
        super(context, mode);
        init();
    }

    public PullToRefreshRecycleView(Context context, Mode mode, AnimationStyle style) {
        super(context, mode, style);
        init();
    }

    private void init(){
        setOnRefreshListener(m_OnRefreshListener);
    }

    @Override
    public Orientation getPullToRefreshScrollDirection() {
        return Orientation.VERTICAL;
    }

    @Override
    protected RecyclerView createRefreshableView(Context context, AttributeSet attrs) {
        view = new RecyclerView(context, attrs);
        view.setId(R.id.discovery_recycle_view);
        return view;
    }

    @Override
    protected boolean isReadyForPullEnd() {
        View view = getRefreshableView().getChildAt(getRefreshableView().getChildCount() - 1);
        if (null != view) {
            return getRefreshableView().getBottom() >= view.getBottom() - 1;
        }
        return false;
    }

    @Override
    protected boolean isReadyForPullStart() {
        View view = getRefreshableView().getChildAt(0);

        if (view != null) {
            return view.getTop() >= getRefreshableView().getTop();
        }
        return false;
    }

    @Override
    public void setOnHeaderRefreshListener(OnHeaderRefreshListener l) {
        m_OnHeaderRefreshListener = l;
    }

    @Override
    public void setOnFooterRefreshListener(OnFooterRefreshListener l) {
        m_OnFooterRefreshListener = l;
    }

    @Override
    public void onHeaderRefreshComplete() {
        onRefreshComplete();
    }

    @Override
    public void onFooterRefreshComplete() {
        onRefreshComplete();
    }

    @Override
    public void setDisableHeaderRefresh(boolean canNot) {
        if (canNot) {
            switch (getMode()) {
                case BOTH:
                    setMode(Mode.PULL_FROM_END);
                    break;
                case PULL_FROM_START:
                    setMode(Mode.DISABLED);
                    break;
            }
        } else {
            switch (getMode()) {
                case BOTH:
                case PULL_FROM_END:
                    setMode(Mode.BOTH);
                    break;
                default:
                    setMode(Mode.PULL_FROM_START);
                    break;
            }
        }
    }

    @Override
    public void setDisableFootRefresh(boolean canNot) {
        if (canNot) {
            switch (getMode()) {
                case BOTH:
                    setMode(Mode.PULL_FROM_START);
                    break;
                case PULL_FROM_END:
                    setMode(Mode.DISABLED);
                    break;
            }
        } else {
            switch (getMode()) {
                case BOTH:
                case PULL_FROM_START:
                    setMode(Mode.BOTH);
                    break;
                default:
                    setMode(Mode.PULL_FROM_END);
                    break;
            }
        }
    }

    @Override
    public void setLastUpdated(CharSequence lastUpdated) {
        getLoadingLayoutProxy().setLastUpdatedLabel(lastUpdated);
    }

    public void setLayoutManager(RecyclerView.LayoutManager layout) {
        if (null != view) {
            view.setLayoutManager(layout);
        }
    }

    public void setAdapter(RecyclerView.Adapter adapter) {
        if (null != view) {
            view.setAdapter(adapter);
        }
    }

    public void addItemDecoration(RecyclerView.ItemDecoration decor) {
        if (null != view) {
            view.addItemDecoration(decor);
        }
    }

    private int getFirstVisiblePosition() {
        View firstVisibleChild = getRefreshableView().getChildAt(0);
        return firstVisibleChild != null ? getRefreshableView()
                .getChildAdapterPosition(firstVisibleChild) : -1;
    }

    private int getLastVisiblePosition() {
        View lastVisibleChild = getRefreshableView().getChildAt(getRefreshableView()
                .getChildCount() - 1);
        return lastVisibleChild != null ? getRefreshableView()
                .getChildAdapterPosition(lastVisibleChild) : -1;
    }

    public void moveToPosition(int n) {
            int firstItem = getFirstVisiblePosition();
            int lastItem = getLastVisiblePosition();
            if (n <= firstItem) {
                getRefreshableView().scrollToPosition(n);
            } else if (n <= lastItem) {
                int top = getRefreshableView().getChildAt(n - firstItem).getTop();
                getRefreshableView().scrollBy(0, top);
            } else {
                getRefreshableView().scrollToPosition(n);
            }
    }
}
```
RecycleView的使用：

```
private PullToRefreshRecycleView discoveryRecycleView;
//设置瀑布流的LayoutManager，还可以实现GridView,ListView等
discoveryRecycleView.setLayoutManager(new StaggeredGridLayoutManager(NUM_COLUMNS,
                StaggeredGridLayoutManager.VERTICAL));

//添加头部View
discoveryAdapter.addHeadView(R.layout.refresh_view_header);

discoveryRecycleView.setAdapter(discoveryAdapter);

//设置分割线样式
discoveryRecycleView.addItemDecoration(new DividerGridItemDecoration(getResources(), R.color.white,
                R.dimen.discovery_item_divider_size, discoveryRecycleView.getOrientation()));


discoveryRecycleView.setDisableHeaderRefresh(false);
discoveryRecycleView.setDisableFootRefresh(false);

discoveryRecycleView.setOnHeaderRefreshListener(mHeaderRefreshListener);
discoveryRecycleView.setOnFooterRefreshListener(mOnFooterRefreshListener);
```

Adapter：

```
public class DiscoveryDetailRecycleViewStageredAdapter extends DiscoveryAdapter {
    private final Logger log = Logger.getLogger(DiscoveryDetailRecycleViewStageredAdapter.class);
    private static final int TYPE_HEADER = 0, TYPE_ITEM = 1, TYPE_FOOT = 2;

    public DiscoveryDetailRecycleViewStageredAdapter(Context context, List<DiscoveryItemModel> discoveryList) {
        super(context, discoveryList);
    }
    
    public void addHeadView(int view) {
        headViewId = view;
        headViewSize = 1;
        isAddHead = true;
    }

    public int hasHeadView() {
        return headViewSize;
    }

    public void addFootView(int view) {
        footViewId = view;
        footViewSize = 1;
        isAddFoot = true;
    }

    @Override
    public DiscoveryItemViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = null;
        switch (viewType) {
            case TYPE_HEADER:
                view = LayoutInflater.from(parent.getContext()).inflate(headViewId, parent, false);
                break;
            case TYPE_ITEM:
                return super.onCreateViewHolder(parent, viewType);
            case TYPE_FOOT:
                view = LayoutInflater.from(parent.getContext()).inflate(footViewId, parent, false);
                break;
        }
        return new DiscoveryItemViewHolder(view);
    }

    @Override
    public void onBindViewHolder(DiscoveryItemViewHolder holder, int position) {
        switch (holder.getItemViewType()) {
            case TYPE_HEADER:
                //将头部View 变为横向铺满
                StaggeredGridLayoutManager.LayoutParams clp = (StaggeredGridLayoutManager.LayoutParams) holder.cardView.getLayoutParams();
                if (clp != null)
                    clp.setFullSpan(true);

                holder.bindHeader(title, subTitle, coverUrl);
                break;

            case TYPE_ITEM:
                super.onBindViewHolder(holder, position);
                break;

            case TYPE_FOOT:
                StaggeredGridLayoutManager.LayoutParams clp1 = (StaggeredGridLayoutManager.LayoutParams) holder.cardView.getLayoutParams();
                clp1.setFullSpan(true);
                break;
        }
    }

    @Override
    public int getItemViewType(int position) {

        int type = TYPE_ITEM;
        if (headViewSize == 1 && position == 0) {
            type = TYPE_HEADER;
        } else if (footViewSize == 1 && position == getItemCount() - 1) {
            //最后一个位置
            type = TYPE_FOOT;
        }
        return type;
    }

    @Override
    public int getItemCount() {
        return discoveryList.size() + headViewSize + footViewSize;
    }

    @Override
    public Object getItem(int position) {
        position-=headViewSize;
        return super.getItem(position);
    }
}
```

分割线实现：

```
public class DividerGridItemDecoration extends RecyclerView.ItemDecoration {
    private Drawable mDivider;
    private int mSize;
    private int mOrientation;

    public DividerGridItemDecoration(Resources resources, @ColorRes int color,
                                     @DimenRes int size, int orientation) {
        mDivider = new ColorDrawable(resources.getColor(color));
        mSize = resources.getDimensionPixelSize(size);
        mOrientation = orientation;
    }

    @Override
    public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
        int left;
        int right;
        int top;
        int bottom;
        if (mOrientation == LinearLayoutManager.HORIZONTAL) {
            top = parent.getPaddingTop();
            bottom = parent.getHeight() - parent.getPaddingBottom();
            final int childCount = parent.getChildCount();
            for (int i = 0; i < childCount - 1; i++) {
                final View child = parent.getChildAt(i);
                final RecyclerView.LayoutParams params =
                        (RecyclerView.LayoutParams) child.getLayoutParams();
                left = child.getRight() + params.rightMargin;
                right = left + mSize;
                mDivider.setBounds(left, top, right, bottom);
                mDivider.draw(c);
            }
        } else {
            left = parent.getPaddingLeft();
            right = parent.getWidth() - parent.getPaddingRight();
            final int childCount = parent.getChildCount();
            for (int i = 0; i < childCount - 1; i++) {
                final View child = parent.getChildAt(i);
                final RecyclerView.LayoutParams params =
                        (RecyclerView.LayoutParams) child.getLayoutParams();
                top = child.getBottom() + params.bottomMargin;
                bottom = top + mSize;
                mDivider.setBounds(left, top, right, bottom);
                mDivider.draw(c);
            }
        }
    }

    @Override
    public void getItemOffsets(Rect outRect, View view, RecyclerView parent,
                               RecyclerView.State state) {
        if (mOrientation == LinearLayoutManager.HORIZONTAL) {
            outRect.set(0, 0, 0, mSize);
        } else {
            outRect.set(0, 0, mSize, 0);
        }
    }
}
```

计算滑动距离
```
private int getScollYDistance() {
        LinearLayoutManager layoutManager = (LinearLayoutManager) showKeyFrameRv.getLayoutManager();
        int position = layoutManager.findFirstVisibleItemPosition();
        View firstVisiableChildView = layoutManager.findViewByPosition(position);
        int itemWidth = firstVisiableChildView.getWidth();
        return (position) * itemWidth - firstVisiableChildView.getLeft();
    }
```

参考博客：
http://blog.csdn.net/mr_wanggang/article/details/46707441
http://blog.csdn.net/lmj623565791/article/details/45059587
http://blog.csdn.net/skykingf/article/details/50827141
https://blog.piasy.com/2016/03/26/Insight-Android-RecyclerView-ItemDecoration/
http://blog.csdn.net/guolin_blog/article/details/10470797/
http://apkbus.com/blog-35555-59152.html
http://blog.csdn.net/xuehuayous/article/details/50394640
http://blog.csdn.net/huangxiaoguo1/article/details/53706971