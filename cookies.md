	你有没有发现过这样一种现象，当你登陆一个网站之后的一段时间内，你第二次打开这个网站时发现不用再次输入账号密码你就已经自动登陆了，或者说当你在淘宝网搜索某些商品时，下次你再搜索的时候发现你上一次搜索的内容在候选框里，这就说明，有一个什么东西把我们的登陆信息或者是搜索的信息给保存起来了，然后下一次打开网站的时候，这个东西会自动帮我们登陆或者记录我们的搜索内容，这个东西就是cookies.
	关于怎么获取cookies，我能想到并且试验过的有三种方法：
1. 既然cookies是存在本地文件的，那我就找出存放的文件名，再查看里面的内容
   我使用的是firefox，这里说一下方法：
        1. 在地址栏输入：about:support，然后你就会看到这样一个界面    
      ![这里写图片描述](http://img.blog.csdn.net/20160929160510948)
    然后找到配置文件夹---->打开目录，找到 cookies.sqlite或者相关文件，这些就是啦

    2. 通过浏览器的工具查看
        还是火狐，按F12，会出现这样的窗口
    ![这里写图片描述](http://img.blog.csdn.net/20160929160900028)
   3. 通过代码获取
   cookies也是可以通过代码获取的，主要使用到了urllib2库和cookielib库，这里将代码贴上来，先不必急着理解，这里只是告诉大家有这么一个方法，具体的情况后面会详细的讲 cookielib 这个模块
   

```
import urllib2
import cookielib
#声明一个CookieJar对象实例来保存cookie
cookie = cookielib.CookieJar()
#利用urllib2库的HTTPCookieProcessor对象来创建cookie处理器
handler=urllib2.HTTPCookieProcessor(cookie)
#通过handler来构建opener
opener = urllib2.build_opener(handler)
#此处的open方法同urllib2的urlopen方法，也可以传入request
response = opener.open('http://www.baidu.com')
for item in cookie:
    print 'Name = '+item.name
    print 'Value = '+item.value
```
代码获取cookie网站一般会有限制。