最近项目做了一个功能， 动态添加View。包括ImageView, TextView等。代码如下：

```
TextView textView = new TextView (context);
textView.setTextSize(TypedValue.COMPLEX_UNIT_SP, size / 2);
textView.setTextColor(getRGB(color));
textView.setSingleLine();
textView.setEllipsize(TextUtils.TruncateAt.END);
textView.setText(text);

LinearLayout.LayoutParams linearLayout = new LinearLayout.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT,
                ViewGroup.LayoutParams.WRAP_CONTENT);
linearLayout.rightMargin = getSize((int) marginRight);
linearLayout.leftMargin = getSize((int) marginLeft);
if (marginBottom != -1) {
    linearLayout.bottomMargin = getSize((int) marginBottom);
    linearLayout.gravity = Gravity.BOTTOM;
} else {
    layoutParams.gravity = Gravity.CENTER_VERTICAL;
}

textView.setLayoutParams(linearLayout);
```

本意是想动态下发textView在父布局中的位置。当设置marginBottom 时，设置textView为在父布局底部，并设置linearLayout.bottomMargin，但是位置并不是想要的。

同样的代码给ImageView设置linearLayout.bottomMargin，值相同但在三星手机上表现距离底部缺相差甚远。搞了好久发现，在其他手机上位置是相同。奔溃。。。。。。。。。。。。。。。

一直以为代码有问题。只能换了一种写法，textView在嵌套一个ViewGroup，该ViewGroup动态设置paddingBottom也可达到该效果。试了一下是想要的效果。。。


三星s6,s7均有该问题。。。。特此记录花费了我半天时间。。。。