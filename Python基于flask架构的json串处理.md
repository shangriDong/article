flask架构进行简单粗略学习，搭建了flask在本机。模拟web service 服务。因为只是针对接口做测试，需要测试桩对接口进行验证，所以选择了flask进行简单学习。
1. 安装Python2.7
2. 通过pip安装需要的虚拟环境隔离工具，具体就是网上说对不同项目进行隔离。pip install virtualenv
3. 安装后执行如下语句virtualenv env,创建env文件夹，注意此时的env创建在了python\Scripts下面了。也可以将virtualenv.exe 安装包拷贝到另外的环境，如E:\flask。 flask为所有功能的总文件夹类似于worksapce。然后执行virtualenv env。
4. 安装flask。执行pip intall flask
5. 因为既有服务端，又存在客户端，所以服务端代码如下：
server.py

```
from flask import Flask,request,Response,jsonify
import json

app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello World!'

@app.route('/json', methods=['POST'])
def my_json():
    print request.headers
    print request.json
    rt = {'info':'hello '+request.json['name']}
    response =Response(json.dumps(rt), mimetype='application/json')
    response.headers.add('Server','python flask')
    #return jsonify({'response':}response)
    return response

if __name__ == '__main__':
    app.run(debug=True)
```

客户端 client.py

```
import requests,json

user_info={'name':'letian'}
headers={'content-type':'application/json'}
r = requests.post("http://127.0.0.1:5000/json",data = json.dumps(user_info),headers=headers)
print r.headers
print r.json()
```

OK ,先运行server.py后会出现如下代码

```
 * Restarting with stat
 * Debugger is active!
 * Debugger pin code: 255-435-829
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```
此时可以执行client代码 `python client.py`

```
{'Date': 'Fri, 26 Aug 2016 08:43:11 GMT', 'Content-Length': '24', 'Content-Type': 'application/json', 'Server': 'python
flask'}
{u'info': u'hello letian'}
```

服务端显示

```
Content-Length: 18
User-Agent: python-requests/2.10.0
Connection: keep-alive
Host: 127.0.0.1:5000
Accept: */*
Content-Type: application/json
Accept-Encoding: gzip, deflate


{u'name': u'letian'}
```

总结：
1. 期间对于客户端可以接收到数据，但是页面访问提示405纠结了好久，在同事帮助下了解到，对于json数据处理只能用post方法，get发放无法传送数据。如果想要在页面显示的话，只需在服务的method方法中，添加get方法。但页面提示错误，只因为get没有获取到request.json的数据
2. 如果客户端执行后提示错误，没有requests模块，则需要安装此模块。pip install requests
暂时学习到这里，后面学习微信公众平台传送接口数据