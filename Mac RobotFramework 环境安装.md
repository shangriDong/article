Mac OS X El Capitan上安装Python自动化测试框架
1，查看当前系统默认的Python路径
 

```
which python
```
==>  /usr/bin/python
2，查看当前python 版本
  

```
python
```
 ==>   Python 2.7.10 (default, Oct 23 2015, 19:19:21) 
3，安装 python 的包管理工具pip

```
curl https://bootstrap.pypa.io/ez_setup.py -o - | python
```

```
easy_install pip
```

4,  安装 brew包管理器

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

5，安装Python(mac已有忽略本步)

`brew install python`  
  ==>   /usr/local/bin/python  版本：2.7.11

6，安装wxPython

```
brew install wxPython
```

7，卸载 robotframework-ride

```
sudo pip uninstall robotframework-ride==2.0a1
```

8，安装robotframework框架

```
sudo pip install robotframework
```

9，安装robotframewor-ride

```
sudo pip install -U robotframework-ride==2.0a1
```

10,安装robotframework-requests

```
sudo pip install robotframework-requests
```

11，安装 requests模块

```
sudo pip install requests
```

 
以上11个步骤是自动化框架安装的基本命令，此时应该能通过命令 ride.py打开RIDE编辑器

12.

```
ride.py
```
即可看到RF可弹出使用