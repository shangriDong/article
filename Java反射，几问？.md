1. 利用android studio 编译一个工程，用来实验反射机制，当使用getDeclaredFields()时，会把所有属性进行打印，其中包括两个在类中没有定义的两个字段："$change","serialVersionUID"？
- "Most likely this field is added in order to support the Instant Run feature added in Android Studio 2.0, and will not appear if you turn off Instant Run.”
https://stackoverflow.com/questions/34647546/a-weird-field-appear-in-android-studio

- http://www.cnblogs.com/biGpython/archive/2012/02/12/2347929.html 解释serialVersionUID      

去掉Instant Run即可解决，Instant Run是什么呢？ 移步：
http://www.jianshu.com/p/2e23ba9ff14b

