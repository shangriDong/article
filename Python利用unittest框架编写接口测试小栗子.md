# Python利用unittest框架编写接口测试
学习接口测试学了大概两三周左右，现在终于可以做一个完整的Python框架的小案例。首先要吐槽的就是网上的接口测试案例真的是太少太少，，，搞不懂为毛，，像无头苍蝇一样转了好久，，都是那么几篇文章。。当时就暗暗下决心，哪天自己学会了，一定要进行总结分享，起码让那些想学习的可以找的参考的资料。接下来说正事。

**接口测试的文档**

每个公司针对不同业务有不同类型的接口，有的传的是xml,有的传的的json,有的传的就是键值对。
![访问地址](http://img.blog.csdn.net/20160927152925256)
![参数](http://img.blog.csdn.net/20160927152955803)
![返回结果](http://img.blog.csdn.net/20160927153009757)

	首先，要有对应的server，如果只是想熟悉接口如何做，OK没问题，如果想能真的访问，那需要跟开发沟通，要一份公司的接口文档去做。
	第二步，接口文档有访问地址，有必须传送参数，和返回结果的参数值。所以我们现在做的就是看访问地址里面，有一个sign.sign通常是签名认证，为了防止非法访问，所以我们做接口就要完全仿照开发向server传送的请求。此时的sign生成函数和规则就要问开发要，他们如何设计的。这里的sign是通过通过key值排序的键值对，用&符号连接，连接后的字符串+ABC ,然后进行加密。
	第三步，写代码啦。
createdSendMessage.py

```
#coding = utf-8
__author__ = 'Administrator'
from collections import OrderedDict

class sendMessage:
    def __init__(self, cname, linkman, email, mobile, address):
        self.cname = cname
        self.linkman = linkman
        self.email = email
        self.mobile = mobile
        self.address = address
        self.send_dsp = {
                'cname': self.cname,
                'linkman': self.linkman,
                'email': self.email,
                'mobile': self.mobile,
                'address': self.address
        }

    def sortedDictKey(self, send_dsp):
        list_messaeg = sorted(send_dsp.keys())
        temp = OrderedDict(sorted(send_dsp.items(), key= lambda t:t[0]))
        return temp, list_messaeg

    def createdDictMessage(self, list_message, temp):
        new_dict = {}
        i = 0
        for list_message[i] in temp:
            new_dict[i] = list_message[i] + '=' + temp.get(list_message[i])
            i = i + 1
        return new_dict

    def combinMessage(self, new_dict,str1):
        j = 0
        str = ''
        for j in  new_dict:
            strname =new_dict.get(j)
            str = str + strname
            if (j + 1) in new_dict:
                str = str +str1
            j = j +1
        return str

    def addYQL(self, str, YQL):
        string1 = str + YQL
        return string1

```

md5Base.py
```
#coding=utf-8
__author__ = 'Administrator'
import hashlib

class md5Encode:
    def md5made(self, string1):
        m = hashlib.md5()
        m.update(string1)
        pws = m.hexdigest()
        return pws
```

运用unittest自然要先引用unittest
base.py

```
#coding=utf-8
__author__ = 'Administrator'
from unittest import TestCase


class BaseJsonTestCase(TestCase):
    def setup(self):
        pass

    def tearDown(self):
        pass
```

创建test函数引用unittest,执行测试函数
test_addCustomer.py

```
#coding=utf-8
__author__ = 'Administrator'
import unittest
from Base.md5Base import md5Encode
from Base.sendMessageBase import sendMessage
from base import BaseJsonTestCase
import requests

class addCustomerTestCase(BaseJsonTestCase):
    def test_send_post_data(self):
        send_dsp = sendMessage('广告主名称', '联系人', '123@qq.com', '15800000000', '四惠')
        sorted_message, list_message = send_dsp.sortedDictKey(send_dsp.send_dsp)
        new_dict =send_dsp.createdDictMessage(list_message,sorted_message)
        str = send_dsp.combinMessage(new_dict,'&')
        string1 = send_dsp.addYQL(str,'ABC')

        md5code = md5Encode()
        pws = md5code.md5made(string1)
        send_dsp.send_dsp['sign'] = pws

        r = requests.post("http://***.***.*.**/**/admin/index.php/oa_api/add_user", data=send_dsp.send_dsp)
        # print r.text
        # print r.status_code
        jsdata = r.json()
        #print int(jsdata['res'])
        self.assertEqual (int(jsdata['res']), 0)

```


总结：
1. 字典排序`temp = OrderedDict(sorted(send_dsp.items(), key= lambda t:t[0]))`
2.  unittest创建base后，测试函数test引用base的类，会自动执行测试函数以test开头。
3. 可以安装nose插件，执行nosetest会执行所有测试用例
4. 使用nosetests自动执行所有test开头的测试函数，需要有assert验证函数，出现异常会报错。
	
