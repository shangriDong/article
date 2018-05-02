发现一个ANR:

```
"main" prio=5 tid=1 Blocked
  | group="main" sCount=1 dsCount=0 obj=0x76f91598 self=0xf4f64500
  | sysTid=16804 nice=-11 cgrp=foreground sched=0/0 handle=0xf7760b50
  | state=S schedstat=( 433512381 42033022 575 ) utm=28 stm=15 core=4 HZ=100
  | stack=0xff7d3000-0xff7d5000 stackSize=8MB
  | held mutexes=
  at java.lang.ProcessManager.exec(ProcessManager.java:206)
  - waiting to lock <0x0388ca78> (a java.util.HashMap) held by thread 22
```

```
"java.lang.ProcessManager" daemon prio=1 tid=22 Waiting
  | group="main" sCount=1 dsCount=0 obj=0x32ea1dc0 self=0xe9f03e00
  | sysTid=16729 nice=19 cgrp=bg_non_interactive sched=0/0 handle=0xda97f930
  | state=S schedstat=( 2809428 380677 7 ) utm=0 stm=0 core=3 HZ=100
  | stack=0xda87d000-0xda87f000 stackSize=1038KB
  | held mutexes=
  at java.lang.Object.wait!(Native method)
  - waiting on <0x0f9eece8> (a java.util.HashMap)
  at java.lang.ProcessManager.waitForMoreChildren(ProcessManager.java:140)
  - locked <0x0f9eece8> (a java.util.HashMap)
  at java.lang.ProcessManager.watchChildren(ProcessManager.java:105)
  at java.lang.ProcessManager.access$000(ProcessManager.java:40)
  at java.lang.ProcessManager$1.run(ProcessManager.java:58)
```

找了一下是因为想要获取CPUInfo信息，使用了ProcessManager cmd.start形式，虽即换了一种写法：

```
private static String getDevicesInfo(final String filePath, final String lookUpFields) {
    if (null == lookUpFields || null == filePath) {
        return null;
    }
    try {
        try {
            Scanner s = new Scanner(new File(filePath));
            while (s.hasNextLine()) {
                String[] vals = s.nextLine().split(": ");
                if (vals.length > 1)  {
                    if (isEqualsFields(lookUpFields, vals[0].trim())) {
                        return vals[1].trim();
                    }
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    } catch (Exception ex) {
        ex.printStackTrace();
    }
    return null;
}
```