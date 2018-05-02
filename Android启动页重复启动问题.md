**问题描述**
当时用 Android 系统installer 安装应用后，点击打开应用，按home键切后台点击icon启动，会导致root activity重复启动。导致应用异常、卡顿等。

**原因**

> Welcome to the ever-growing list of users who have been bitten by this one.
This is a well-known and long-standing Android bug. in the way applications get launched the first time from the installer, web-browser and via IDE (IntelliJ, Eclipse, etc.). See these issues filed long ago related to the problem:
http://code.google.com/p/android/issues/detail?id=2373
http://code.google.com/p/android/issues/detail?id=26658
It is still broken and you cannot prevent this from happening. The only thing you can do is to detect when Android has launched a second instance of your root activity into an existing task. You can do this by putting this code in onCreate() of your root activity:

**解决办法**
在root activity的onCreate()中添加这句话即可解决。
```
if (!isTaskRoot()) {
    // Android launched another instance of the root activity into an existing task
    //  so just quietly finish and go away, dropping the user back into the activity
    //  at the top of the stack (ie: the last state of this task)
    finish();
    return;
}
```

```
/**
 * Return whether this activity is the root of a task.  The root is the
 * first activity in a task.
 *
 * @return True if this is the root activity, else false.
 */
 // 返回当前activity是否为task的根activity,root意味着在是在task中的第一个activity.
public boolean isTaskRoot() {
    try {
        return ActivityManagerNative.getDefault().getTaskForActivity(mToken, true) >= 0;
    } catch (RemoteException e) {
        return false;
    }
}
```

引用链接：
https://stackoverflow.com/questions/16283079/re-launch-of-activity-on-home-button-but-only-the-first-time/16447508#16447508


相关链接：
https://blog.csdn.net/cppalien/article/details/41958767
https://blog.csdn.net/u013039472/article/details/53284312
https://blog.csdn.net/singwhatiwanna/article/details/9294285

附录：
下面是一些异常日志信息，

通过下面命令可以常看到当前应用的Activity堆栈情况
```
adb shell dumpsys activity activities | grep com.package.name
```

```
Activities=[ActivityRecord{232df185 u0 com.package.name/.ui.welcome.LaunchActivity t135}, ActivityRecord{328bcab9 u0 com.package.name/.ui.main.MainActivity t135}, ActivityRecord{3ef044dc u0 com.package.name/.ui.room.RoomActivity t135}, ActivityRecord{339781a4 u0 com.package.name/.ui.show.ShowActivity t135}, ActivityRecord{15a663ed u0 com.package.name/.ui.welcome.LaunchActivity t135}]
      * Hist #4: ActivityRecord{15a663ed u0 com.package.name/.ui.welcome.LaunchActivity t135}
          packageName=com.package.name processName=com.package.name
          app=ProcessRecord{31ce96e8 16228:com.package.name/u0a148}
          Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10600000 cmp=com.package.name/.ui.welcome.LaunchActivity (has extras) }
          frontOfTask=false task=TaskRecord{2d6e9434 #135 A=com.package.name U=0 sz=5}
          taskAffinity=com.package.name
          realActivity=com.package.name/.ui.welcome.LaunchActivity
          baseDir=/data/app/com.package.name-1/base.apk
          dataDir=/data/data/com.package.name
      * Hist #3: ActivityRecord{339781a4 u0 com.package.name/.ui.show.ShowActivity t135}
          packageName=com.package.name processName=com.package.name
          launchedFromUid=10148 launchedFromPackage=com.package.name userId=0
          app=ProcessRecord{31ce96e8 16228:com.package.name/u0a148}
          Intent { cmp=com.package.name/.ui.show.ShowActivity (has extras) }
          frontOfTask=false task=TaskRecord{2d6e9434 #135 A=com.package.name U=0 sz=5}
          taskAffinity=com.package.name
          realActivity=com.package.name/.ui.show.ShowActivity
          baseDir=/data/app/com.package.name-1/base.apk
          dataDir=/data/data/com.package.name
      * Hist #2: ActivityRecord{3ef044dc u0 com.package.name/.ui.room.RoomActivity t135}
          packageName=com.package.name processName=com.package.name
          launchedFromUid=10148 launchedFromPackage=com.package.name userId=0
          app=ProcessRecord{31ce96e8 16228:com.package.name/u0a148}
          Intent { cmp=com.package.name/.ui.room.RoomActivity (has extras) }
          frontOfTask=false task=TaskRecord{2d6e9434 #135 A=com.package.name U=0 sz=5}
          taskAffinity=com.package.name
          realActivity=com.package.name/.ui.room.RoomActivity
          baseDir=/data/app/com.package.name-1/base.apk
          dataDir=/data/data/com.package.name
      * Hist #1: ActivityRecord{328bcab9 u0 com.package.name/.ui.main.MainActivity t135}
          packageName=com.package.name processName=com.package.name
          launchedFromUid=10148 launchedFromPackage=com.package.name userId=0
          app=ProcessRecord{31ce96e8 16228:com.package.name/u0a148}
          Intent { flg=0x4000000 cmp=com.package.name/.ui.main.MainActivity }
          frontOfTask=false task=TaskRecord{2d6e9434 #135 A=com.package.name U=0 sz=5}
          taskAffinity=com.package.name
          realActivity=com.package.name/.ui.main.MainActivity
          baseDir=/data/app/com.package.name-1/base.apk
          dataDir=/data/data/com.package.name
      * Hist #0: ActivityRecord{232df185 u0 com.package.name/.ui.welcome.LaunchActivity t135}
          packageName=com.package.name processName=com.package.name
          app=ProcessRecord{31ce96e8 16228:com.package.name/u0a148}
          Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10000000 pkg=com.package.name cmp=com.package.name/.ui.welcome.LaunchActivity }
          frontOfTask=true task=TaskRecord{2d6e9434 #135 A=com.package.name U=0 sz=5}
          taskAffinity=com.package.name
          realActivity=com.package.name/.ui.welcome.LaunchActivity
          baseDir=/data/app/com.package.name-1/base.apk
          dataDir=/data/data/com.package.name
      TaskRecord{2d6e9434 #135 A=com.package.name U=0 sz=5}
        Run #9: ActivityRecord{15a663ed u0 com.package.name/.ui.welcome.LaunchActivity t135}
      TaskRecord{2d6e9434 #135 A=com.package.name U=0 sz=5}
        Run #7: ActivityRecord{339781a4 u0 com.package.name/.ui.show.ShowActivity t135}
        Run #6: ActivityRecord{3ef044dc u0 com.package.name/.ui.room.RoomActivity t135}
        Run #5: ActivityRecord{328bcab9 u0 com.package.name/.ui.main.MainActivity t135}
        Run #4: ActivityRecord{232df185 u0 com.package.name/.ui.welcome.LaunchActivity t135}
    mResumedActivity: ActivityRecord{15a663ed u0 com.package.name/.ui.welcome.LaunchActivity t135}
  mFocusedActivity: ActivityRecord{15a663ed u0 com.package.name/.ui.welcome.LaunchActivity t135}
```

可以看到LaunchActivity activity存在两个，这里的LaunchActivity就是root activity，是不应该存在多个的。
第一次启动的intent

```
Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10000000 pkg=com.package.name cmp=com.package.name/.ui.welcome.LaunchActivity }
```
flag为FLAG_ACTIVITY_NEW_TASK 0x10000000。

```
/**
 * If set, this activity will become the start of a new task on this
 * history stack.  A task (from the activity that started it to the
 * next task activity) defines an atomic group of activities that the
 * user can move to.  Tasks can be moved to the foreground and background;
 * all of the activities inside of a particular task always remain in
 * the same order.  See
 * <a href="{@docRoot}guide/topics/fundamentals/tasks-and-back-stack.html">Tasks and Back
 * Stack</a> for more information about tasks.
 *
 * <p>This flag is generally used by activities that want
 * to present a "launcher" style behavior: they give the user a list of
 * separate things that can be done, which otherwise run completely
 * independently of the activity launching them.
 *
 * <p>When using this flag, if a task is already running for the activity
 * you are now starting, then a new activity will not be started; instead,
 * the current task will simply be brought to the front of the screen with
 * the state it was last in.  See {@link #FLAG_ACTIVITY_MULTIPLE_TASK} for a flag
 * to disable this behavior.
 *
 * <p>This flag can not be used when the caller is requesting a result from
 * the activity being launched.
 */
// 如果设置了，将会在历史堆栈中新启动一个task,activity为这个task的第一个元素。task(从当前activity
// 开始到下个task监理)定义了一个Activity元子组，用户可以进行移动。tasks可以从前后台进行切换。
// 在指定的task中的所有activity, 始终保持相同的顺序。
// 这个flag通常使用在，activity想要以launcher的形式出现；他们会给用户一个单独的列表，否则完全独立运行与
// 启动他们的activity。
// 当使用这个flag时，如果一个task已经运行了你正在启动的activity，那么一个新的activity不会开始;代替的是
// 当前的task只需要带着最后的状态运行到前台。
// 当调用者想要从当前启动的activity获得结果，是不能使用这个flag的。
public static final int FLAG_ACTIVITY_NEW_TASK = 0x10000000;
```

而在第二次启动的LaunchActivity时intent为：

```
Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10600000 cmp=com.package.name/.ui.welcome.LaunchActivity (has extras) }
```
falg=FLAG_ACTIVITY_BROUGHT_TO_FRONT&FLAG_ACTIVITY_RESET_TASK_IF_NEEDED&FLAG_ACTIVITY_NEW_TASK

```
/**
 * This flag is not normally set by application code, but set for you by
 * the system as described in the
 * {@link android.R.styleable#AndroidManifestActivity_launchMode
 * launchMode} documentation for the singleTask mode.
 */
/**
 * 这个flag通常不需要由应用程序代码设置，但是系统会在singleTask模式中设置。
 */
public static final int FLAG_ACTIVITY_BROUGHT_TO_FRONT = 0x00400000;

/**
 * If set, and this activity is either being started in a new task or
 * bringing to the top an existing task, then it will be launched as
 * the front door of the task.  This will result in the application of
 * any affinities needed to have that task in the proper state (either
 * moving activities to or from it), or simply resetting that task to
 * its initial state if needed.
 */
public static final int FLAG_ACTIVITY_RESET_TASK_IF_NEEDED = 0x00200000;
```