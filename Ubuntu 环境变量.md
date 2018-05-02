转载自：http://www.linuxidc.com/Linux/2016-09/135476.htm
使用Ubuntu 进行开发绕不开的就是环境变量的配置，由于Linux系统严格的权限管理，造成Ubuntu系统有多个环境变量配置文件，如果不了解其调用顺序，很有可能遇到配置了环境变量，而没有其作用的问题。本文将介绍Ubuntu Linux系统的环境变量。

一、UbuntuLinux系统环境变量配置文件

Ubuntu Linux系统环境变量配置文件分为两种：系统级文件和用户级文件，下面详细介绍环境变量的配置文件。

1.系统级文件：

/etc/profile:在登录时,操作系统定制用户环境时使用的第一个文件，此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行。并从/etc/profile.d目录的配置文件中搜集shell的设置。这个文件一般就是调用/etc/bash.bashrc文件。

/etc/bash.bashrc：系统级的bashrc文件，为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.

/etc/environment: 在登录时操作系统使用的第二个文件,系统在读取你自己的profile前,设置环境文件的环境变量。

2.用户级文件：

~/.profile: 在登录时用到的第三个文件 是.profile文件,每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。

~/.bashrc:该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取。不推荐放到这儿，因为每开一个shell，这个文件会读取一次，效率 上讲不好。

~/.bash_profile：每个用户都可使用该文件输入专用于自己 使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。~/.bash_profile 是交互式、login 方式进入 bash 运行的~/.bashrc是交互式 non-login 方式进入 bash 运行的通常二者设置大致相同，所以通常前者会调用后者。

~./bash_login:不推荐使用这个，这些不会影响图形界面。而且.bash_profile优先级比bash_login高。当它们存在时，登录shell启动时会读取它们。

~/.bash_logout:当每次退出系统(退出bash shell)时,执行该文件.

~/.pam_environment：用户级的环境变量设置文件。

另外,/etc/profile中设定的变量(全局)的可以作用于任何用户,而~/.bashrc等中设定的变量(局部)只能继承 /etc/profile中的变量,他们是"父子"关系。 

二、/etc/profile与/etc /enviroment的比较

首先来做一个实验:

先将export LANG=zh_CN加入/etc/profile ,退出系统重新登录，登录提示显示英文。将/etc/profile中的export LANG=zh_CN删除，将LNAG=zh_CN加入/etc/environment，退出系统重新登录，登录提示显示中文。 

用户环境建立的过程中总是先执行/etc/profile然后在读取/etc/environment。为什么会有如上所叙的不同呢？ 

应该是先执行/etc/environment，后执行/etc/profile。 

/etc/environment是设置整个系统的环境，而/etc/profile是设置所有用户的环境，前者与登录用户无关，后者与登录用户有关。

系统应用程序的执行与用户环境可以是无关的，但与系统环境是相关的，所以当你登录时，你看到的提示信息，比如日期、时间信息的显示格式与系统环境的LANG是相关的，缺省LANG=en_US，如果系统环境LANG=zh_CN，则提示信息是中文的，否则是英文的。 

对于用户的SHELL初始化而言是先执行/etc/profile, 再读取文件/etc/environment. 

对整个系统而言是先执行/etc/environment。这样理解正确吗？ 

/etc/enviroment -->/etc/profile --> $HOME/.profile -->$HOME/.env (如果存在) 

/etc/profile 是所有用户的环境变量

/etc/enviroment是系统的环境变量 

登陆系统时shell读取的顺序应该是

/etc/profile ->/etc/enviroment -->$HOME/.profile-->$HOME/.env 

原因应该是用户环境和系统环境的区别了 

如果同一个变量在用户环境(/etc/profile)和系统环境(/etc/environment) 有不同的值那应该是以用户环境为准了。 

备注：在shell中执行程序时，shell会提供一组环境变量。export可新增，修改或删除环境变量，供后续执行的程序使用。export的效力仅及于该此登陆操作。 

在登录Linux时要执行文件的过程如下：

在刚登录Linux时，首先启动/etc/profile 文件，然后再启动用户目录下的 ~/.bash_profile、 ~/.bash_login或 ~/.profile文件中的其中一个，执行的顺序为：~/.bash_profile、 ~/.bash_login、 ~/.profile。如果 ~/.bash_profile文件存在的话，一般还会执行 ~/.bashrc文件。因为在 ~/.bash_profile文件中一般会有下面的代码：

 

if[ -f ~/.bashrc ] ; then
　../bashrc
　　　　　　　　　　　fi
　　~/.bashrc中，一般还会有以下代码：
if[ -f /etc/bashrc ] ; then
　./bashrc
fi
 

所以，~/.bashrc会调用/etc/bashrc文件。最后，在退出shell时，还会执行~/.bash_logout文件。 

执行顺序为：/etc/profile -> (~/.bash_profile | ~/.bash_login | ~/.profile) -> ~/.bashrc-> /etc/bashrc -> ~/.bash_logout 

三、设置环境变量的方法

由以上分析可知：

**/etc/profile全局的，随系统启动设置【设置这个文件是一劳永逸的办法】**

/root/.profile和/home/myname/.profile只对当前窗口有效。

/root/.bashrc和 /home/yourname/.bashrc随系统启动，设置用户的环境变量【平时设置这个文件就可以了】

那么要配置Ubuntu的环境变量，就是在这几个配置文件中找一个合适的文件进行操作了；如想将一个路径加入到$PATH中，可以由下面这样几种添加方法：

1.控制台中：

$PATH="$PATH:/my_new_path"    （关闭shell，会还原PATH）

2.修改profile文件：

$sudo gedit /etc/profile

在里面加入:

exportPATH="$PATH:/my_new_path"

3.修改.bashrc文件：

$ sudo gedit /root/.bashrc

在里面加入：

export PATH="$PATH:/my_new_path"

后两种方法一般需要重新注销系统才能生效，最后可以通过echo命令测试一下：

$ echo $PATH

输出已经是新路径了。

举个列子，如果想把当前路径加入到环境变量中去，就可以这样做：

$  PATH ="$PATH:."

这样运行自己编写的shell脚本时就可以不输入./了

四、小结

综上所述，在Ubuntu 系统中/etc/profile文件是全局的环境变量配置文件，它适用于所有的shell。在我们登陆Linux系统时，首先启动/etc/profile文件，然后再启动用户目录下的~/.bash_profile、~/.bash_login或~/.profile文件中的其中一个，执行的顺序和上面的排序一样。如果~/.bash_profile文件存在的话，一般还会执行~/.bashrc文件。


----------

```
export ANDROID_NDK=/home/shangri/Android/android-ndk-r13b
export ANDROID_SDK=/home/shangri/Android/Sdk
export PATH=$PATH:$ANDROID_SDK/tools:$ANDROID_SDK/platform-tools:$ANDROID_NDK/

//保存后执行
source /etc/profile
```

