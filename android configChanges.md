转载：

对android:configChanges属性，一般认为有以下几点：

 

1、不设置Activity的android:configChanges时，切屏会重新调用各个生命周期，切横屏时会执行一次，切竖屏时会执行两次

 

 

2、设置Activity的android:configChanges="orientation"时，切屏还是会重新调用各个生命周期，切横、竖屏时只会执行一次

 

3、设置Activity的android:configChanges="orientation|keyboardHidden"时，切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法
 

但是，自从Android 3.2（API 13），在设置Activity的android:configChanges="orientation|keyboardHidden"后，还是一样 会重新调用各个生命周期的。因为screen size也开始跟着设备的横竖切换而改变。所以，在AndroidManifest.xml里设置的MiniSdkVersion和 TargetSdkVersion属性大于等于13的情况下，如果你想阻止程序在运行时重新加载Activity，除了设置"orientation"， 你还必须设置"ScreenSize"。

解决方法：

AndroidManifest.xml中设置android:configChanges="orientation|screenSize“
我是设置如下

android:configChanges="orientation|screenSize|keyboardHidden"

加上这个设置之后尼，只要Activity不打开了，屏幕旋转时，不会再重新执行onCreate等，如果是旋转的Activity处于前台，那么旋转屏幕不会执行任何的生命方法，只会执行

@Override

public void onConfigurationChanged(Configuration newConfig)

方法

通过代码设置横屏，竖屏：
```
setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT); //竖屏
setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE); //横屏
```

```
//判断当前是否为竖屏模式
private boolean isPortraitMode() {
        if (this.getResources().getConfiguration().orientation == Configuration.ORIENTATION_PORTRAIT) {
            return true;
        } else {
            return false;
        }
    }
```