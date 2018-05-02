android自带工具：
\sdk\tools\monitor

启动后，插入手机界面如下：
![这里写图片描述](http://img.blog.csdn.net/20171019194036864?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

选择黑色框中的线程，
选中后，上不菜单变为可点状态：
![这里写图片描述](http://img.blog.csdn.net/20171019194242853?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
点击 1 处（Start Method Profiing） 按钮，开始收集数据。

操作想要测试的步骤。

之后继续点击 该按钮停止收集，会自动弹出相关数据：
![这里写图片描述](http://img.blog.csdn.net/20171019194653800?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

之后分析该数据
点击查看项目会有二级选项：
![这里写图片描述](http://img.blog.csdn.net/20171019194812973?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

两个菜单为 Parents和Children，顾名思义是该函数的堆栈上下级调用关系。

当在全部折叠状态，在Incl Cpu Time列 有100%选项，说明该函数为可疑函数，可能会阻塞主线程，即可点击查看，并查看Children项，一层一层找到相关项目！