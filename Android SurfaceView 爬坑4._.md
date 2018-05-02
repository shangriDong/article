最近做项目要用到SurfaceView，当在4.*机型时（比如4.2,4.1等）会出现当应用切后台，在回来时SurfaceView重新创建会把原先得布局覆盖。

造成该问题有两个解决办法或造成的原因：
1. 当.xml 定义如下

```
<SurfaceView />
<ImageView />
```
当在xml中定义其先后顺序为先是sruface 后是imageView。加载的时候也要严格按照这个顺序，可以避免该问题的出现。

2.当SurfaceView在fragment中的使用如果想要隐藏掉，**不可以直接设置为View.GONE,** 需要设置为**View.INVISIBLE**，可以避免当切后台再回来把其他布局掩盖的问题。。