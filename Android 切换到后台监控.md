近日做了个需求，要求应用切到后台停止部分服务，切回后继续。
下面 记录一下方法：

```
//activity中
public boolean isAppOnForeground() {
        // Returns a list of application processes that are running on the
        // device

        ActivityManager activityManager = (ActivityManager) getApplicationContext().getSystemService(Context.ACTIVITY_SERVICE);
        String packageName = getApplicationContext().getPackageName();

        List<ActivityManager.RunningAppProcessInfo> appProcesses = activityManager
                .getRunningAppProcesses();
        if (appProcesses == null)
            return false;

        for (ActivityManager.RunningAppProcessInfo appProcess : appProcesses) {
            // The name of the process that this object is associated with.
            if (appProcess.processName.equals(packageName)
                    && appProcess.importance == ActivityManager.RunningAppProcessInfo.IMPORTANCE_FOREGROUND) {
                return true;
            }
        }

        return false;
    }
```
AppStateManage.java

```
public class AppStateManage extends Observable{
    private AppState m_iAppState = AppState.INIT;
    private static AppStateManage instance;

    private AppStateManage() {
    }

    public static AppStateManage getInstance() {
        if (instance == null) {
            synchronized (AppStateManage.class) {
                if (instance == null) {
                    instance = new AppStateManage();
                }
            }
        }
        return instance;
     }

    public void changeAppState(AppState appState) {
        m_iAppState = appState;
        notifyChange(appState);
    }

    private void notifyChange(AppState appState) {
        setChanged();
        notifyObservers(appState);
    }

    public enum AppState {
        INIT,
        RESUME,
        GO_BACKGROUND;
    }

    public boolean isGoBackgroundState() {
        if (m_iAppState == AppState.GO_BACKGROUND) {
            return true;
        }

        return false;
    }

    public AppState getAppState() {
        return m_iAppState;
    }
}
```

activity抽象类中的使用：

```
	@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (this instanceof SplashActivity) {
            // 如果是第一次启动应用 清一下通知栏
            AppStateManage.getInstance().changeAppState(AppStateManage.AppState.INIT);
        } else {
            AppStateManage.getInstance().changeAppState(AppStateManage.AppState.RESUME);
        }
    }
    
	@Override
    protected void onResume() {
        super.onResume();

        if (AppStateManage.getInstance().isGoBackgroundState()) {
            // 如果是从后台切换到前台 清一下通知栏
            AppStateManage.getInstance().changeAppState(AppStateManage.AppState.RESUME);
        } else {
            AppStateManage.getInstance().changeAppState(AppStateManage.AppState.RESUME);
        }
    }
    
	@Override
    protected void onStop() {
        super.onStop();
        m_iStop = 1;
        if (!isAppOnForeground()) {
	        AppStateManage.getInstance().changeAppState(AppStateManage.AppState.GO_BACKGROUND);
        }
    }
```