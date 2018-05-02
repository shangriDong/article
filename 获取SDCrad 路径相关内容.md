# 获取SDCrad 路径相关内容

感谢老铁博客 http://blog.csdn.net/nugongahou110/article/details/48154859

自己做了一下总结。

- App 专属路径
 1. 函数：getExternalFilesDir()    路径： /sdcardf/Android/data/***/   解释： 存储在external storage
 2. 函数：getFilesDir()                 路径：/data/data/***/                      解释：  存储在internal storage

- App 独立文件
获取独立文件夹路径：
1. Environment.getExternalStorageDirectory()
2. Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES)