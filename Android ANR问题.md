拉取traces.txt
 adb pull /data/anr/traces.txt .
 将该文件拉取

1. JNI层引用JNI ERROR (app bug): local reference table overflow (max=512) 没有释放查过512
```
08-13 14:46:00.432 14119-16343/com.vv51.vvlive E/dalvikvm: JNI ERROR (app bug): local reference table overflow (max=512)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm: JNI local reference table (0x77699010) dump:
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:   Last 10 entries (of 512):
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       511: 0x43640f28 byte[] (153 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       510: 0x4335aba8 byte[] (147 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       509: 0x43292750 byte[] (153 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       508: 0x462197c8 byte[] (147 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       507: 0x461e72e8 byte[] (153 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       506: 0x4610fd20 byte[] (147 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       505: 0x4605edf8 byte[] (147 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       504: 0x45f9f1a8 byte[] (153 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       503: 0x45f27348 byte[] (147 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       502: 0x45ede8c8 byte[] (150 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:   Summary:
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:         1 of java.lang.Class
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:         2 of byte[] (9 elements) (2 unique instances)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:         1 of byte[] (60 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       199 of byte[] (66 elements) (199 unique instances)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:         1 of byte[] (138 elements)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:         7 of byte[] (141 elements) (7 unique instances)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:         7 of byte[] (142 elements) (7 unique instances)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:        48 of byte[] (144 elements) (48 unique instances)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:        94 of byte[] (147 elements) (94 unique instances)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:        48 of byte[] (150 elements) (48 unique instances)
08-13 14:46:00.442 14119-16343/com.vv51.vvlive W/dalvikvm:       102 of byte[] (153 elements) (102 unique instances)
```
请移步改博客：
http://blog.csdn.net/xyang81/article/details/44873769