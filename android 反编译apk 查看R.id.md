工具介绍：
apktool  
     作用：主要查看res文件下xml文件、AndroidManifest.xml和图片。（注意：如果直接解压.apk文件，xml文件打开全部是乱码）
dex2jar
     作用：将apk反编译成Java源码（classes.dex转化成jar文件）
jd-gui
     作用：查看APK中classes.dex转化成出的jar文件，即源码文件


```
apktool d test.apk
```

之后全局搜索id

```
grep -nr "str"
```

 [工具包地址](https://code.google.com/archive/p/innlab/downloads)