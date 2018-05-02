转自 ： [http://blog.csdn.net/ameyume/article/details/7528862](http://blog.csdn.net/ameyume/article/details/7528862)

SharedPreferences存储共享变量的文件路径位于“/data/data/应用程序包/shared_prefs”目录下，通过adb shell，可以看到如下所示：
查看当前目录：

```
# pwd
```

/data/data/com.min.ijoke/shared_prefs
显示当前目录下的文件：

```
# ls
```

min_ijoke.xml // 此文件就是存储SharedPreferences变量的文件

```
AppSettings.xml
PushFlag.xml
Finalize_Flag.xml
ShowAdFlag.xml
Start_Tag.xml
```

查看SharedPreferences变量的文件内容，都是键值对形式存储在xml文件中的。
以下是我的程序中使用到的变量和值：

```
# cat min_ijoke.xml
```

**手机必须root, 要不然看不到！！！**