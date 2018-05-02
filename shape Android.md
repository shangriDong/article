转载自：http://www.cnblogs.com/cyanfei/archive/2012/07/27/2612023.html
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    
    <!-- 圆角 -->
    <corners
        android:radius="9dp"
        android:topLeftRadius="2dp"
        android:topRightRadius="2dp"
        android:bottomLeftRadius="2dp"
        android:bottomRightRadius="2dp"/><!-- 设置圆角半径 -->
    
    <!-- 渐变 -->
    <gradient
        android:startColor="@android:color/white"
        android:centerColor="@android:color/black"
        android:endColor="@android:color/black"
        android:useLevel="true"
        android:angle="45"
        android:type="radial"
        android:centerX="0"
        android:centerY="0"
        android:gradientRadius="90"/>
    
    <!-- 间隔 -->
    <padding
        android:left="2dp"
        android:top="2dp"
        android:right="2dp"
        android:bottom="2dp"/><!-- 各方向的间隔 -->
    
    <!-- 大小 -->
    <size
        android:width="50dp"
        android:height="50dp"/><!-- 宽度和高度 -->
    
    <!-- 填充 -->
    <solid
        android:color="@android:color/white"/><!-- 填充的颜色 -->
    
    <!-- 描边 -->
    <stroke
        android:width="2dp"
        android:color="@android:color/black"
        android:dashWidth="1dp"
        android:dashGap="2dp"/>
    
</shape>
```

填充：设置填充的颜色

间隔：设置四个方向上的间隔

大小：设置大小

圆角：同时设置五个属性，则Radius属性无效

android:Radius="20dp"                           设置四个角的半径

android:topLeftRadius="20dp"              设置左上角的半径 
android:topRightRadius="20dp"           设置右上角的半径 
android:bottomLeftRadius="20dp"      设置右下角的半径 
android:bottomRightRadius="20dp"    设置左下角的半径

描边：dashWidth和dashGap属性，只要其中一个设置为0dp，则边框为实现边框

android:width="20dp"                               设置边边的宽度 
android:color="@android:color/black"  设置边边的颜色 
android:dashWidth="2dp"                         设置虚线的宽度 
android:dashGap="20dp"                          设置虚线的间隔宽度

渐变：当设置填充颜色后，无渐变效果。angle的值必须是45的倍数（包括0），仅在type="linear"有效，不然会报错。android:useLevel 这个属性不知道有什么用。

angle对应值的起点如图：
![这里写图片描述](http://img.blog.csdn.net/20161108193251724)