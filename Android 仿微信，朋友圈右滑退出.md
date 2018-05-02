直接上代码：

```
public class SlideLinearLayout extends LinearLayout {
    private Logger log = Logger.getLogger(SlideLinearLayout.class.getName());
    private Scroller mScroller;
    private ViewGroup mParentView;
    private float downX;
    private float downY;
    private float tempX;
    private int viewWidth;
    private boolean isSilding;
    private int mTouchSlop;
    private boolean isFinish;

    private int MIN_DISTANCE;

    private float mLastMotionX;
    private float mLastMotionY;
    private int mActivePointerId = INVALID_POINTER;
    private boolean mIsBeingDragged = false;

    private static final int INVALID_POINTER = -1;

    private OnListener onListener;

    public SlideLinearLayout(Context context) {
        super(context);
        init();
    }

    public SlideLinearLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public SlideLinearLayout(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init();
    }

    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
        super.onLayout(changed, l, t, r, b);
        if (changed) {
            mParentView = (ViewGroup) this.getParent();
            viewWidth = this.getWidth();
        }
    }

    /**
     * 滚动出界面
     */
    private void scrollRight() {
        final int delta = (viewWidth + mParentView.getScrollX());
        // 调用startScroll方法来设置一些滚动的参数，我们在computeScroll()方法中调用scrollTo来滚动item
        mScroller.startScroll(mParentView.getScrollX(), 0, -delta + 1, 0,
                Math.abs(delta));
        postInvalidate();
    }

    /**
     * 滚动到起始位置
     */
    private void scrollOrigin() {
        int delta = mParentView.getScrollX();
        mScroller.startScroll(mParentView.getScrollX(), 0, -delta, 0,
                Math.abs(delta));
        postInvalidate();
    }

    private void init() {
        mTouchSlop = ViewConfiguration.get(getContext()).getScaledTouchSlop();
        mScroller = new Scroller(getContext());
        MIN_DISTANCE = DisplayUtil.dip2px(getContext(), 30);
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        log.debug("onInterceptTouchEvent event: " + ev.getAction());
        final int action = ev.getAction() & MotionEventCompat.ACTION_MASK;
        if (action == MotionEvent.ACTION_CANCEL || action == MotionEvent.ACTION_UP) {
            // Release the drag.
            resetTouch();
            return false;
        }

        if (action != MotionEvent.ACTION_DOWN) {
            if (mIsBeingDragged) {
                return true;
            }
        }
        switch (action) {
            case MotionEvent.ACTION_MOVE: {
                final int activePointerId = mActivePointerId;
                if (activePointerId == INVALID_POINTER) {
                    // If we don't have a valid id, the touch down wasn't on content.
                    break;
                }

                final int pointerIndex = ev.findPointerIndex(activePointerId);
                final float x = ev.getX(pointerIndex);
                final float xDiff = Math.abs(x - mLastMotionX);

                if (xDiff > mTouchSlop && mLastMotionX <= MIN_DISTANCE) {
                    mIsBeingDragged = true;
                    return true;
                }
                break;
            }
            case MotionEvent.ACTION_DOWN: {
                downX = tempX = mLastMotionX = ev.getX();
                downY = mLastMotionY = ev.getY();
                mActivePointerId = ev.getPointerId(0);
                break;
            }
        }
        return super.onInterceptTouchEvent(ev);
    }

    private void resetTouch() {
        mLastMotionX = 0;
        mLastMotionY = 0;
        mActivePointerId = INVALID_POINTER;
        mIsBeingDragged = false;
        tempX = 0;
        downX = 0;
        downY = 0;
    }

    @Override
    public void computeScroll() {
        // 调用startScroll的时候scroller.computeScrollOffset()返回true，
        if (mScroller.computeScrollOffset()) {
            mParentView.scrollTo(mScroller.getCurrX(), mScroller.getCurrY());
            postInvalidate();
        }
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        log.info("onTouchEvent " + event.getAction());
        switch (event.getAction()) {
            case MotionEvent.ACTION_MOVE:
                int moveX = (int) event.getRawX();
                int deltaX = (int) (tempX - moveX);
                tempX = moveX;
                if (Math.abs(moveX - downX) > mTouchSlop) {
                    isSilding = true;
                }

                if (moveX - downX >= 0 && isSilding) {
                    mParentView.scrollBy(deltaX, 0);
                    log.info("moveX: " + moveX + " deltaX: " + deltaX);
                } else {
                    log.error("moveX: " + (moveX - downX) + " isSilding: " + isSilding);
                }
                break;
            case MotionEvent.ACTION_UP:
                isSilding = false;
                if (event.getRawX() > viewWidth / 2) {
                    isFinish = true;
                    scrollRight();

                    if (onListener != null) {
                        onListener.OnFinish();
                    }
                } else {
                    scrollOrigin();
                    isFinish = false;
                }
                resetTouch();
                break;
            case MotionEvent.ACTION_CANCEL:
                break;
        }
        return true;
    }

    public void setOnListener(OnListener onListener) {
        this.onListener = onListener;
    }

    public interface OnListener {
        void OnFinish();
    }
}
```

将此布局设置为activity根布局，即可实现效果。
**注意要把activity 的背景设置为透明。**

```
<style name="transparent_background" parent="@style/Theme.AppCompat.NoActionBar">
        <item name="android:windowBackground">@color/transparent_color</item>
        <item name="android:windowNoTitle">true</item>
        <item name="android:windowIsTranslucent">true</item>
</style>
```
可利用以上代码进行设置！