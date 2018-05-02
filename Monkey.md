**Monkey测试的特点**主要有以下几点：
可对MonkeyTest的对象，事件数量，类型，频率等进行设置。
Monky测试使用的事件流数据流是随机的，不能进行自定义。
测试的对象仅为应用程序包，有一定的局限性。

**Monkey命令**
1
-v
作用：命令行上的每一个-v都将增加反馈信息的详细级别。
Level0（默认），除了启动、测试完成和最终结果外只提供较少的信息。
Level1，提供了较为详细的测试信息，如逐个发送到Activity的事件信息。
Level2，提供了更多的设置信息，如测试中选中或未选中的Activity信息。
例：

```
adb shell monkey -v 10
adb shell monkey -v -v 10
adb shell monkey -v -v -v 10
```

2
-s <seed>
作用：伪随机数生成器的seed值。如果用相同的seed值再次运行monkey，将生成相同的事件序列。
例：`adb shell monkey -s 12345 -v 10`

3
--throttle <milliseconds>
作用：在事件之间插入固定的时间（毫秒）延迟，你可以使用这个设置来减缓Monkey的运行速度，如果你不指定这个参数，则事件之间将没有延迟，事件将以最快的速度生成。
例：`adb shell monkey –throttle 300 -v 10`
注：常用参数，一般设置为300毫秒，原因是实际用户操作的最快300毫秒左右一个动作事件，所以此处一般设置为300毫秒。

4
--pct-touch <percent>
作用：调整触摸事件的百分比。（触摸事件是指在屏幕中的一个down-up事件，即在屏幕某处按下并抬起的操作）
例：`adb shell monkey –pct-touch 100 -v 10`
注：常用参数，此参数设置要适应当前被测应用程序的操作，比如一个应用80%的操作都是触摸，那就可以将此参数的百分比设置成相应较高的百分比。

5
--pct-motion <percent>
作用：调整motion事件百分比。（motion事件是由屏幕上某处一个down事件、一系列伪随机的移动事件和一个up事件组成）
例：`adb shell monkey –pct-motion 100 -v 10`
注：常用参数，需注意的是移动事件是直线滑动，下面的trackball移动包含曲线移动。

6
--pct-trackball <percent>
作用：调整滚动球事件百分比。（滚动球事件由一个或多个随机的移动事件组成，有时会伴随着点击事件）
例：`adb shell monkey –pct-trackball 100 -v 10`
注：不常使用参数，现在手机几乎没有滚动球，但滚动球事件中包含曲线滑动事件，在被测程序需要曲线滑动时可以选用此参数。

7
--dbg-no-events
作用：设置此选项，Monkey将执行初始启动，进入一个测试Activity，并不会在进一步生成事件。为了得到最佳结果，结合参数-v，一个或多个包的约束，以及一个保持Monkey运行30秒或更长时间的非零值，从而提供了一个可以监视应用程序所调用的包之间转换的环境。

8
--kill-process-after-error
作用：通常，当Monkey由于一个错误而停止时，出错的应用程序将继续处于运行状态。设置此项，将会通知系统停止发生错误的进程。注意，正常（成功）的结束，并没有停止启动的进程，设备只是在结束事件之后简单的保持在最后的状态。

9
--monitor-native-crashes
作用：监视并报告Andorid系统中本地代码的崩溃事件。如果设置–kill-process-after-error，系统将停止运行。

10
记录测试日志
保存测试日志其实很简单，命令如下：

```
adb shell monkey -p com.ihongqiqu -v -v -v 500 > monkeytest.txt
```

