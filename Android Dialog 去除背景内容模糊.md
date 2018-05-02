定义一个style

```
	<style name="disable_background_dim_dialog" parent="@android:style/Theme.Dialog">
        <item name="android:windowFrame">@null</item> <!-- 边框 -->
        <item name="android:windowIsFloating">true</item> <!-- 是否浮现在activity之上 -->
        <item name="android:windowIsTranslucent">false</item> <!-- 半透明 -->
        <item name="android:windowNoTitle">true</item> <!-- 无标题 -->
        <item name="android:backgroundDimEnabled">false</item> <!-- 背景内容模糊 -->
    </style>
```

在代码中进行设置：

```
setStyle(STYLE_NORMAL, R.style.disable_background_dim_dialog);
```

即可成功解决