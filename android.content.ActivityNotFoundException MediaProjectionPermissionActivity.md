项目爆出一个错误，录屏时崩溃 手机型号：朵唯L9系统是5.1.1 

先介绍一下MediaProjectionPermissionActivity有什么功能：
http://www.freebuf.com/vuls/81905.html

崩溃是因为该手机系统就没有这个Activity. 同样问题机型好友HUAWEI Y560，原因不详，stackoverflow也有介绍：
https://stackoverflow.com/questions/37541293/activitynotfoundexception-unable-to-find-explicit-activity-class-mediaprojection?noredirect=1&lq=1

解决办法：

```
try {
	act.startActivityForResult(mMediaProjectionManager.createScreenCaptureIntent(), requestCode);
} catch (ActivityNotFoundException exception) {
    
}
```