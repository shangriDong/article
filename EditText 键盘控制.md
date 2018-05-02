在使用EditText进行文本输入时，若不进行特殊的设置，使用Android自带的软键盘,该软键盘会占用整个界面，那么，如何让键盘只占用屏幕的一部分呢？ 

```
<EditText   
    android:id="@+id/text1"   
    android:layout_width="150dip"   
    android:layout_height="wrap_content"  
    android:imeOptions="flagNoExtractUi"/>  
```

```
android:imeOptions="flagNoExtractUi"  //使软键盘不全屏显示，只占用一部分屏幕  
//同时,这个属性还能控件软键盘右下角按键的显示内容,默认情况下为回车键  
android:imeOptions="actionNone"  //输入框右侧不带任何提示  
android:imeOptions="actionGo"    //右下角按键内容为'开始'  
android:imeOptions="actionSearch"  //右下角按键为放大镜图片，搜索  
android:imeOptions="actionSend"    //右下角按键内容为'发送'  
android:imeOptions="actionNext"   //右下角按键内容为'下一步'  
android:imeOptions="actionDone"  //右下角按键内容为'完成'  
```

同时，可能EditText添加相应的监听器，捕捉用户点击了软键盘右下角按钮的监听事件，以便进行处理。

```
editText.setOnEditorActionListener(new OnEditorActionListener() {   
        @Override  
        public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {   
            Toast.makeText(MainActivity.this, "text2", Toast.LENGTH_SHORT).show();   
            return false;   
        }   
    });  
```