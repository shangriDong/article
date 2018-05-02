1 打印线程id
```
Thread.currentThread().getId()
```

2 打印堆栈

```
Log.info(Log.getStackTraceString(new Throwable()));
```

3.天气访问接口

```
接口地址：
http://www.weather.com.cn/data/sk/101010100.html
http://www.weather.com.cn/data/cityinfo/101010100.html
http://m.weather.com.cn/data/101010100.html
```

4豆瓣250连接

```
https://api.douban.com/v2/movie/top250?start=0&count=10
```

5 判断当前是否允许横竖屏切换

```
private boolean isAutoRotateOn(Context context) {
        return android.provider.Settings.System.getInt(context.getContentResolver(),        Settings.System.ACCELEROMETER_ROTATION, 0) == 1 ;
    }
```
6 Fragment&ViewPager
I encountered the similar issue as you, after several hours research, I found the issue. If your ViewPager is created inside the Fragment, you need to use getChildFragmentManager() for the FragmentPagerAdapter, rather than getFragmentManager().

So your setupViewPager() method should look like this.
```
private void setupViewPager(ViewPager viewPager) {
    ViewPagerAdapter adapter = new ViewPagerAdapter(getChildFragmentManager());
    adapter.addFrag(new Courses(), "IT");
    adapter.addFrag(new CoursesBussiness(), "Business");
    viewPager.setAdapter(adapter);
}
```
The root cause of the issue is you embeded Fragments inside the Fragment, that's why it will throw the exception.

```
java.lang.IllegalStateException: FragmentManager is already executing transactions
```
According to the Google's docs:

> You can now embed fragments inside fragments. This is useful for a variety of situations in which you want to place dynamic and re-usable UI components into a UI component that is itself dynamic and re-usable. For example, if you use ViewPager to create fragments that swipe left and right and consume a majority of the screen space, you can now insert fragments into each fragment page. To nest a fragment, simply call getChildFragmentManager() on the Fragment in which you want to add a fragment. This returns a FragmentManager that you can use like you normally do from the top-level activity to create fragment transactions.