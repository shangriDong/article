发先一个ViewGroup超好用的属性。
**android:clipChildren** 是否裁剪子布局
```
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.dqs.shangri.clipchildren.MainActivity"
    android:clipChildren="false">


    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="20dp"
        android:layout_marginTop="200dp"
        android:background="@color/colorAccent"
        android:gravity="bottom">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello World!"
            android:layout_gravity="center_vertical"/>

        <ImageView
            android:layout_width="30dp"
            android:layout_height="30dp"
            android:background="@color/colorPrimary"/>
    </LinearLayout>

</FrameLayout>

```

效果图：
![这里写图片描述](http://img.blog.csdn.net/20170731150748184?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

其中蓝色部分已经超出了父布局。
奇妙无比，可以完成许多设计想出的方案。。。。。。。。。。


为什么会发现这个东西呢。是因为最近做个自定义布局。会自动填充内容，长度不可预期，内容为一个textView,文案长度不固定。之后会做一个动画从屏幕右方票到屏幕左方。


一切都做好了，但是动画播出后，ViewGroup被裁剪了，只剩下屏幕宽度。所以才找出了这个属性。但是单凭这个属性还不够，还有另外一个步骤就是：**自定义的这个布局要继承HorizontalScrollView**。

 * HorizontalScrollView 水平超出屏幕范围；
 * ScrollView  当竖直超出屏幕范围。


```
android:clipChildren="false"
```
改属性一定写到跟布局。


下面是源码：

```
public class MosaicBgViewGroup extends HorizontalScrollView {
    private Logger logger = Logger.getLogger(getClass());

    private AnimatorSet animatorSet;
    private RelativeLayout rootView;
    private View childView;
    private LinearLayout loadCarryingBgLy;
    private LinearLayout contentLy;
    private ImageView headerView;
    private ImageView tailView;

    private int headerViewResId;
    private int tailViewResId;
    private int middleViewResId;

    private int middleViewMarginLeft = DisplayUtil.dip2px(getContext(), 25);

    private int tailViewEffectiveSpace = DisplayUtil.dip2px(getContext(), 0.5f);

    public MosaicBgViewGroup(Context context) {
        super(context);
    }

    public MosaicBgViewGroup(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public MosaicBgViewGroup(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    public void initResId(@DrawableRes int headerViewResId, @DrawableRes int tailViewResId,
                          @DrawableRes int middleViewResId) {
        this.headerViewResId = headerViewResId;
        this.tailViewResId = tailViewResId;
        this.middleViewResId = middleViewResId;
    }

    public void setMiddleViewMarginLeft(int middleViewMarginLeft) {
        this.middleViewMarginLeft = middleViewMarginLeft;
    }

    public void setTailViewEffectiveSpace(int tailViewEffectiveSpace) {
        this.tailViewEffectiveSpace = tailViewEffectiveSpace;
    }

    public void addChildView(View view) {
        if (view == null || headerViewResId == 0 || tailViewResId == 0 || middleViewResId == 0) {
            logger.error("add child view is null!");
            return;
        }
        removeAllViews();
        rootView = new RelativeLayout(getContext());

        childView = view;
        contentLy = new LinearLayout(getContext());
        contentLy.setOrientation(LinearLayout.HORIZONTAL);

        initHeaderView();
        initTailView();
        initLoadCarryingBgLy();

        rootView.addView(loadCarryingBgLy);
        contentLy.addView(headerView);
        contentLy.addView(childView);
        rootView.addView(contentLy);

        addView(rootView);
    }

    public void runAnim(AnimatorSet animatorSet) {
        if (animatorSet == null) {
            logger.error("run anim error, anim null!");
            return;
        }
        this.animatorSet = animatorSet;
        this.animatorSet.start();
    }

    private void initHeaderView() {
        headerView = new ImageView(getContext());
        headerView.setScaleType(ImageView.ScaleType.CENTER);
        headerView.setImageResource(headerViewResId);
    }

    private void initTailView() {
        tailView = new ImageView(getContext());
        tailView.setScaleType(ImageView.ScaleType.CENTER);
        tailView.setImageResource(tailViewResId);
    }

    private void initLoadCarryingBgLy() {
        loadCarryingBgLy = new LinearLayout(getContext());
        loadCarryingBgLy.setPadding(middleViewMarginLeft, 0, 0, 0);
        loadCarryingBgLy.setOrientation(LinearLayout.HORIZONTAL);
        for (int i = 0; i < getMiddleCount(); i++) {
            ImageView view = new ImageView(getContext());
            view.setScaleType(ImageView.ScaleType.CENTER);
            view.setBackgroundResource(middleViewResId);

            loadCarryingBgLy.addView(view);
        }
        loadCarryingBgLy.addView(tailView);
    }

    private int getChildViewWidth(View view) {
        int spec = View.MeasureSpec.makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED);
        view.measure(spec,spec);
        return view.getMeasuredWidth();
    }

    private int getMiddleCount() {
        int diff = ((getChildViewWidth(childView) % getMiddleWidth()) >= tailViewEffectiveSpace ? 1 : 0) + 1;
        return (getChildViewWidth(childView) / getMiddleWidth()) + diff;
    }

    private int getMiddleWidth() {
        View view = new View(getContext());
        view.setBackgroundResource(middleViewResId);
        return getChildViewWidth(view);
    }
}
```