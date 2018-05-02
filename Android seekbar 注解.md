```
<SeekBar
	android:id="@+id/beauty_seek_bar_brightness"
	android:layout_width="261dp"
	android:layout_height="wrap_content"
	android:background="@null"
	android:max="100"
	android:maxHeight="4dp"
	android:minHeight="4dp"
	android:progress="100"
	android:progressDrawable="@drawable/beauty_face_seek_bar"
	android:splitTrack="false"
	android:thumb="@drawable/beauty_face_move_bg" />
```


----------


```
android:progressDrawable="@drawable/beauty_face_seek_bar"
```
添加 进度条颜色

beauty_face_seek_bar 如下：

```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
	<!-- 背景图 -->
    <item android:id="@android:id/background">
        <shape>
            <solid android:color="#cecece" />
        </shape>
    </item>
    <!-- 第二进度图 -->
    <item android:id="@android:id/secondaryProgress">
        <clip>
            <shape>
                <solid android:color="#cecece" />
            </shape>
        </clip>
    </item>
    <!-- 进度度 -->
    <item android:id="@android:id/progress">
        <clip>
            <shape>
                <solid android:color="#39d7ba" />
            </shape>
        </clip>
    </item>
</layer-list>
```
----------
```
android:splitTrack="false"
```
![](http://img.blog.csdn.net/20160903174918990)
第一个为android:splitTrack 为默认时显示样式，
第二个为设置为false的样式。

----------
```
android:thumb="@drawable/beauty_face_move_bg"
```
添加移动的按钮的图片


----------

```
android:background="@null"
```
改属性很有必要， 在5.0以上如果不设置，按下后移动按钮后 会有黄色或其他颜色的圈
