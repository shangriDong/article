android:drawable 放一个drawable资源
android:state_pressed 是否按下，如一个按钮触摸或者点击。
android:state_focused 是否取得焦点，比如用户选择了一个文本框。
android:state_hovered 光标是否悬停，通常与focused state相同，它是4.0的新特性
android:state_selected 被选中，它与focus state并不完全一样，如一个list view 被选中的时候，它里面的各个子组件可能通过方向键，被选中了。
android:state_checkable 组件是否能被check。如：RadioButton是可以被check的。
android:state_checked 被checked了，如：一个RadioButton可以被check了。
android:state_enabled 能够接受触摸或者点击事件
android:state_activated 被激活(这个麻烦举个例子，不是特明白)
android:state_window_focused 应用程序是否在前台，当有通知栏被拉下来或者一个对话框弹出的时候应用程序就不在前台了


android:shape=["rectangle" | "oval" | "line" | "ring"]  
shape的形状，默认为矩形，可以设置为矩形（rectangle）、椭圆形(oval)、线性形状(line)、环形(ring)  
下面的属性只有在android:shape="ring时可用：  
android:innerRadius         尺寸，内环的半径。  
android:innerRadiusRatio    浮点型，以环的宽度比率来表示内环的半径，  
android:thickness           尺寸，环的厚度  
android:thicknessRatio      浮点型，以环的宽度比率来表示环的厚度，例如，如果android:thicknessRatio="2"，  
android:useLevel            boolean值，如果当做是LevelListDrawable使用时值为true，否则为false. 