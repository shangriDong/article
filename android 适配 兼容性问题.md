一、带有动画的fragment间跳转，会有抖动问题。

只需两步即可解决：
 onCreate添加
  getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
  //透明导航栏
  getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_NAVIGATION);


二、VerticalViewPager有抖动解决方案：
xml中添加：
android:fitsSystemWindows="true"

三、魅族等手机键盘变黑，或按home键不收起键盘

android:theme="@style/JK.SwipeBack.Transparent.Theme"
android:windowSoftInputMode="stateUnspecified|adjustUnspecified"

四、Mi3动画有残影
	view.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
	
一个View可以使用如下的三种layer类型之一：
◆ LAYER_TYPE_NONE: 这个View将被按普通的方式进行渲染，但是不会返回一个离屏的缓冲，这个是默认的行为。
◆ LAYER_TYPE_HARDWARE:如果这个应用被硬件加速的话，这个View将会在硬件中渲染为硬件纹理，如果应用程序并没有被硬件加速，则其效果和LAYER_TYPE_SOFTWARE是相同的。
◆ LAYER_TYPE_SOFTWARE: 此View 通过软件渲染为一个bitmap。

mi3 动画有拖影

```
 @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        super.onCreateView(inflater, container, savedInstanceState);
        mInflater = inflater;
        mDensity = getResources().getDisplayMetrics().density;
        root = (RelativeLayout) inflater.inflate(R.layout.fragment_show_interaction_danmu, null);
        if (!root.isHardwareAccelerated()) {
            root.setLayerType(View.LAYER_TYPE_HARDWARE, null); //解决问题的关键
        }
        return root;
    }
```

五、4.X新加属性
导航栏设置
```
	if(Build.VERSION.SDK_INT >= 21){
	    root.setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION);
	}
```
六、当键盘升起，View 被压缩
当子视图 为RelativeLayout时，即会被压缩，修改为LinearLayout即可解决该问题