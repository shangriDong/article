最近学习Python接口测试，对于接口测试完全小白。大概一周的学习成果进行总结。
1.接口测试：
	目前涉及到的只是对简单单一的接口进行参数传递，得到返回自。
2.关于各种概念：
	2.1 http请求包含post方法、get方法。通过json串或XML传递，但后者未做研究
	2.2 GET: 浏览器告诉服务器，只**获取**页面信息，并发送给我。
	2.3 POST：浏览器告诉服务器想法不一些信息到某个网址，服务器需确保数据被存储且只存储一次。
	2.4 HEAD：浏览器告诉服务器，给我消息头，像get那样被接收。
	2.5 Python对数据的处理模块可以使用urllib、urllib2模块或requests模块
3.urllib、urllib2实例

```
#coding=utf_8
import urllib2,urllib
import json
import unittest,time,re

class APITest():
	"""
	接口测试类
	"""
	def api_test(self, method, url, getparams, postparams):
		str1 = ''

		#GET方法调用
		if method == 'GET':
			if getparams != "":
				for x in getparams:
					str1 = str1 + x + '=' + urllib2.quote(str(getparams.get(x)))
					if len(getparams) > 2:
						str1 = str1 + "&"
				url = url + "&" + str1

			result = urllib2.urlopen(url).read()

		#POST方法调用
		if method=='POST':
			if postparams != "":
				data = urllib.urlencode(postparams)
				req = urllib2.Request(data)
			response = urllib2.urlopen(req)
			result = response.read()

		#result转为json数据
		jsdata = json.loads(result)
		return jsdata

class APIGetRes(unittest.TestCase):
	def test_call(self):
		api = APITest()
		getparams={'keyword':'测试'}
		postparams=''
		data = api.api_test('GET','http://api.zhongchou.cn/deal/list?v=1',getparams,postparams)
		print data
		if (data['errno']!=""):
			self.assertEqual(0, data['errno'])
			print"接口 deal/list-------------OK!"
		else:
			print"接口 deal/list-------------Failure!"
			self.assertEqual(0, data['errno'])

if __name__ == '__main__':
	unittest.main()
```

Requests实例
```
#coding=utf_8
import requests
import json
import unittest,time,re


class APIGetAdlis(unittest.TestCase):
	def test_call(self):
		github_url='http://api.zhongchou.cn/deal/list?v=1'
		data = json.dumps({'keyword':'测试'})
		resp = requests.post(github_url,data)
		print resp.json
		#if (data['errno']!=''):
		#	self.assertEqual(0, data['errno'])
		#	print"接口 deal/list-------------OK!"
		#else:
		#	print"接口 deal/list-------------Failure!"
		#	self.assertEqual(0, data['errno'])
```
粗略了解，待深入学习