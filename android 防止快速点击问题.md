防止快速点击， 记录两次点击时间差
```
private static long mLastClickTime;
public static boolean isFastDoubleClick(){
    long time  = System.currentTimeMillis();
    long timeD = time - mLastClickTime;
    if(timeD > 0 && timeD < 500){
        return true;
    }
    mLastClickTime = time;
    return false;
}
```