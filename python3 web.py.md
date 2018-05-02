https://stackoverflow.com/questions/41304192/python-alternative-for-web-py-on-python3

Python3 安装 web.py 失败： 
```
import utils, db, net, wsgi, http, webapi, httpserver, debugerror
    ImportError: No module named 'utils'
```
按一下办法解决：
```
pip3 install web.py==0.40.dev0
```

Python3 安装MySQL
http://www.runoob.com/python3/python3-mysql.html

```
pip3 install PyMySQL
```


----------


改错误：
ERROR 1698 (28000): Access denied for user 'root'@'localhost'

首先查看mysql的conf文件查看用户名和密码：

```
/etc/mysql
```
展示如下：
```
conf.d  debian.cnf  debian-start  my.cnf  my.cnf.fallback  mysql.cnf  mysql.conf.d
```
执行`sudo cat debian.cnf`
展示如下：
```
# Automatically generated for Debian scripts. DO NOT TOUCH!
[client]
host     = localhost
user     = debian-sys-maint
password = XG6IscrXa4gslb4n
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = XG6IscrXa4gslb4n
socket   = /var/run/mysqld/mysqld.soc
```

其中user和password即为你的mysql的用户名和密码。再次链接数据库 即可成功！


----------

```
ERROR (1366, "Incorrect string value: '\\xE6\\x8F\\x8F\\xE8\\xBF\\xB0' for column 'projectDescribe' at row 2")

ERROR (1064, "You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '1.1.0.0\\xe6\\x8f\\x8f\\xe8\\xbf\\xb0'', isUse = 0 WHERE md5 = '339a150f79a8c8d125ad8c' at line 1

ERROR 'latin-1' codec can't encode characters in position 62-63: ordinal not in range(256)
```
这个错误为，str插入数据库，str有中文参与，需要数据库进行支持，解决办法：

```
cur.execute("CREATE DATABASE %s default charset utf8 collate utf8_unicode_ci" % operationDB.dbName)
```
**default charset utf8 collate utf8_unicode_ci** 至关重要！

其次，每一次连接数据库也需要设置charset：

```
pymysql.connect("localhost", dbUser, dbPassword, dbName, charset="utf8")
```
综上即可解决该问题！


----------
