# Android 系统动画， 移动中不能点击问题！


Android 3.3 之后加入属性动画，即可解决，移动中可以对View进行点击。View动画则不接收在移动的位置进行点击。  

利用View动画也想要接收点击事件请往下看：


- Android动画因为在移动中不接受点击事件，采取View 盖住动画，通过动画运动轨迹 计算点击处 是否为 移动的图片所在的实时位置，从而达到 实现点击的效果。

-------------------
https://github.com/shangriDong/AnimClick.git

这是全部代码。。


关键在于要计算当前动画A的运算轨迹， 非匀速运动动画此方法不可处理。

```java
private void animStart(View view) {
        view.measure(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
        mViewWidth = view.getMeasuredWidth();
        widthPixels = mDm.widthPixels;

        mDurationInScreen = (long) (widthPixels / (widthPixels + mViewWidth) * mDuration); //计算 在屏幕内移动的时间

        //从屏幕的右侧，漂移到屏幕左侧
        ObjectAnimator animator = ObjectAnimator.ofFloat(view, View.TRANSLATION_X, (int) widthPixels, -(int) mViewWidth);
        animator.setDuration(mDuration); //移动10s
        animator.setInterpolator(new LinearInterpolator()); //匀速移动 必须为允许，否则 计算位置将不准确
        mStartTime = System.currentTimeMillis();
        animator.start();
    }
```

此函数用来处理 动画的移动

```java
animator.setInterpolator(new LinearInterpolator()); //匀速移动 必须为允许，否则 计算位置将不准确
```
一定要 设置为匀速动画， 如果自己写差值器， 也可以计算出移动位置。

```java
view.measure(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
        mViewWidth = view.getMeasuredWidth();
        widthPixels = mDm.widthPixels;
```

此代码用来获取动画的宽度，必须要measure，否咋不能正确的获得view的宽度，因为view没有被绘制

```java
mBottomView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {

                if (event.getAction() == MotionEvent.ACTION_UP) {
                    double diffTime = System.currentTimeMillis() - mStartTime;
                    if (diffTime < mDurationInScreen) {
                        int x = (int) ((1 - diffTime / mDurationInScreen) * widthPixels);
                        if (event.getX() >= x && event.getX() <= (x + mViewWidth)) {
                            Log.e(getClass().getName(), "---------------success------");
                            Toast.makeText(activity, "success", Toast.LENGTH_LONG).show();
                        }
                    }
                }
                return true; //此处必然为true, 否则接收不到up 事件
            }
        });
```

onTouch时间一定要return true 否则不会接受到MotionEvent.ACTION_UP 事件。



**sy_dqs@163.com 欢迎指正，谢谢！ 原创博客，不经博主同意不得转载。**