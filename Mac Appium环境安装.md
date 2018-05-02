Appium的环境安装实在是太坑爹了，，，国外appium安装命令不成功，各种搜索问题，，现在已经成功安装，出现问题就不停的Google吧。Google更换hosts文件即可翻墙。

1. java JDK安装
	http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html 安装对应的JDK后，添加到环境变量
	

```
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_111.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```
java -v 判断java是否安装成功
2. Git安装
3. Ruby安装
	安装RF时已经安装了Ruby
4. brew
	同样，安装RF时已经安装brew
5. Xcode
	若要使用appium1.5.3版本，则Xcode版本要8以下
	下载地址：https://developer.apple.com/downloads/
6. Android SDK
	下载地址：https://developer.android.com/studio/index.html#downloads
选择：android-sdk_r24.4.1-macosx.zip（写本文时的最新版）解压缩到任意位置，比如/usr/local/android-sdk-macosx下。
运行/usr/local/android-sdk-macosx/tools/android，即可启动Android SDK Manager。
![这里写图片描述](http://img.blog.csdn.net/20160601102612456)
![这里写图片描述](http://img.blog.csdn.net/20160601102758911)
Accept License。然后Install就可以了。这个过程根据网速不同，可能需要10-20分钟，耐心等待。
7. 设置环境变量
```
vi ~/.bash_profile

export ANDROID_HOME=/usr/local/android-sdk-macosx
export PATH=$PATH:$ANDROID_HOME/platform-tools:$ANDROID_HOME/tools
source ~/.bash_profile
```
8. Appium 安装
	appium两种安装方式，一种是直接下载dmg，解压后安装，有界面，所有代码也都在界面中显示。另一种方式命令行安装，，执行脚本后，代码全部显示在命令行中。无论哪种，都依赖于nodejs
所以下安装nodeJs，此处以命令行安装为例
1). node
```
brew install node
```
如果在这过程中无法安装node，也可以直接下载nodejs一步一步安装，再执行此条命令
2). appium
```
brew install -g appium@1.5.3
```
此处执行安装appium，若幸运，则中途不报错正常安装，然而这是很低很低很低的概率。查找若干文档后，发现可以替代这个命令的。但在说这个之前，，肯定在执行这条命令的时候报错了，，提示没有权限操作/usr/local/....云云，这个时候，需要给对应没权限的文件夹以执行权限。不能用sudo 命令安装，即便安装了，也无法正常使用appium。

```
chmod 777 /usr/local/**...
```
若是还不行，看一下是不是当前用户没有管理员的操作权限，对应报错的文件夹给予管理员权限，并根据提示做链接

```
chown -R USERNAME /User/USERNAME/***
```
 当文件夹权限问题解决后，就是安装appium的时候了，，若有VPN则可直接访问国外网站使用上面命令安装appium，若没有VPN则使用万能的淘宝镜像来替代
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
```
cnpm install -g appium@1.5.3  #get appium
```
3). client 安装
```
npm install wd    #get appium client
```
4). appium server
```
appium &     # start appium
```
至此 Appium安装完了。

5). appium-doctor启动
appium doctor 用来验证appium安装是否成功。在终端执行
```
npm install appium-doctor -g
```
输入appium-doctor检测环境是否成功。
6). appium-client安装
```
pip install appium-python-client
``` 

**Appium使用**
对于初学，安装好了以后仍然都是命令行的东西，，不知道如何使用，，，win下的appium都是有界面的啊，Mac下没界面的appium着实让我感觉无从下手的样子，不知道appium & 和appium-doctor有什么区别。目前的了解时appium & 是启动appium server。而appium-doctor 只是验证appium安装问题。appium-client才是在我们每次编辑项目时候要用到的appium。
1). 打开appiumserver

```
appium --session-override
```
连接真机后，执行这条语句，开启appium监视器，监听客户端，并打印log

2). 下载安装android包，获取APK名称
获取到要测试的android包，将它写到代码中如：android-v2.6.0-dev.apk

3). 安装android包
adb命令將android 包安装到测试机

4). 编写程序

```
from appium import webdriver
import time
from selenium.webdriver.common.by import By

desired_caps = {}
desired_caps['platformName'] = 'Android'
desired_caps['platformVersion'] = '6.0.1' 
＃android版本
desired_caps['deviceName'] = 'M4'＃机器名称
desired_caps['app'] = '/Users/USERNAME/Downloads/android-v2.6.0-dev.apk'＃APK路径


wd = webdriver.Remote('http://0.0.0.0:4723/wd/hub', desired_caps)
wd.implicitly_wait(60)

def is_alert_present(wd):
	try:
		wd.switch_to_alert().text
		return True
	except:
		return False

try:
    #print wd.page_source
    time.sleep(10)
    ＃for循环模拟手指滑动启示引导页
    for i in range(5):
        wd.swipe(start_x=1000, start_y=200, end_x=0, end_y=200)
wd.find_element_by_id('tutorial_page_open_speedx').click()
＃锁定引导页上的某个button并点击
    time.sleep(10)
    # wd.find_elements_by_android_uiautomator('new UiSelector().resourceId("authentication_activity_form_switch")').click()
    # wd.find_element_by_id('com.beastbikes.android:id/authentication_activity_form_switch').click()
    # wd.find_elements_by_android_uiautomator('new UiSelector().clickable(true)')
    #wd.find_element_by_xpath('//UIAStaticText[@name="Already have an account?"]').click()
    #wd.find_element_by_xpat.h("//UIAApplication[1]/UIAWindow[1]/UIATableView[1]/UIATableCell[1]/UIATextField[1]").send_keys("15727388185")
    # wd.find_element_by_xpath('//XCUIElementTypeTextField[@value="Phone / Email"]').send_keys("15727388185")
    # wd.find_element_by_xpath('//XCUIElementTypeSecureTextField[@value="Please enter your password"]').send_keys("123456")

finally:
	wd.quit()
```
不是我的APK代码肯定执行不通，pycharm上直接执行代码即可看到在监控的log

5). 使用uiautomatorviewer工具定位元素
android定位元素使用uiautomatorviewer工具定位元素，工具在androidSDK的tools下执行 `./uiautomatorviewer` 或者在/usr/bin目录下执行  `uiautomatorviewer`


