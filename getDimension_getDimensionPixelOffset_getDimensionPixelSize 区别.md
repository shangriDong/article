自己要顶一个View, 进行属性配置发现有三个方法，在网上找了一下 貌似说的不是很准确，自己实践了一下：

dimens.xml 配置
```
<dimen name="round_count_down_view_dp">1.5dp</dimen>
<dimen name="round_count_down_view_px">1.5px</dimen>
<dimen name="round_count_down_view_sp">1.5sp</dimen>
```

```
max = getResources().getDimension(R.dimen.round_count_down_view_dp);
Log.d("RoundCountDownView", "getDimension dp: " + max);
max = getResources().getDimension(R.dimen.round_count_down_view_px);
Log.d("RoundCountDownView", "getDimension: px:" + max);
max = getResources().getDimension(R.dimen.round_count_down_view_sp);
Log.d("RoundCountDownView", "getDimension: sp:" + max);

max = getResources().getDimensionPixelOffset(R.dimen.round_count_down_view_dp);
Log.d("RoundCountDownView", "getDimensionPixelOffset dp: " + max);
max = getResources().getDimensionPixelOffset(R.dimen.round_count_down_view_px);
Log.d("RoundCountDownView", "getDimensionPixelOffset: px:" + max);
max = getResources().getDimensionPixelOffset(R.dimen.round_count_down_view_sp);
Log.d("RoundCountDownView", "getDimensionPixelOffset: sp:" + max);

max = getResources().getDimensionPixelSize(R.dimen.round_count_down_view_dp);
Log.d("RoundCountDownView", "getDimensionPixelSize dp: " + max);
max = getResources().getDimensionPixelSize(R.dimen.round_count_down_view_px);
Log.d("RoundCountDownView", "getDimensionPixelSize: px:" + max);
max = getResources().getDimensionPixelSize(R.dimen.round_count_down_view_sp);
Log.d("RoundCountDownView", "getDimensionPixelSize: sp:" + max);
```

结果：

```
D/RoundCountDownView: getDimension dp: 4.5
D/RoundCountDownView: getDimension: px:1.5
D/RoundCountDownView: getDimension: sp:4.5
D/RoundCountDownView: getDimensionPixelOffset dp: 4.0
D/RoundCountDownView: getDimensionPixelOffset: px:1.0
D/RoundCountDownView: getDimensionPixelOffset: sp:4.0
D/RoundCountDownView: getDimensionPixelSize dp: 5.0
D/RoundCountDownView: getDimensionPixelSize: px:2.0
D/RoundCountDownView: getDimensionPixelSize: sp:5.0
```

最后根据结果可以清晰知道三种获取dimens的区别：
getDimension：px不需要乘以density, dp、sp 均需要乘以density，并且结果为float，不会四舍五入

getDimensionPixelOffset：px不需要乘以density, dp、sp 均需要乘以density，**区别于getDimension会进行向下取舍。**

getDimensionPixelSize：px不需要乘以density, dp、sp 均需要乘以density， **区别于getDimension会进行向上取舍。**