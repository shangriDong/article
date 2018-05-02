# flask传送post参数和token

1. server端代码
**gen_token**(uid): 使用base64 进行编码，存储在users中，添加在list列表"123456"字符串后面
**vertify_token**(token)：将传进来的token进行base64的解码，解码后如果users.get(_token.split(':')[0])[-1] == token获取users中的token与参数进行比对，如果不相等，返回-1.如果一致再进行token时间的比对后返回1，否则返回0
**login**()：登录函数。将headers传过来的参数users 进行拆分，拆分后解码分别赋值uid,pw, 判断uid对应的password与pw是否相同，相同则将token存储在users中
**test**()：解密后的token与当前参数token比对，验证token是否正确后返回data
```
import base64
import random
import time
from flask import Flask, request
import json

app = Flask(__name__)

users = {
    "magigo":["123456"]
}

def gen_token(uid):
    token = base64.b64encode(':'.join([str(uid), str(random.random()), str(time.time() + 7200)]))
    users[uid].append(token)
    return token

def vertify_token(token):
    _token = base64.b64decode(token)
    #print "_token:"+_token
    if not users.get(_token.split(':')[0])[-1] == token:
        print "users_token:" +users.get(_token.split(':')[0])[-1]
        print "users" + json.dumps(users)

        return -1
    if float(_token.split(':')[-1]) >= time.time():
        return 1
    else:
        return 0


@app.route('/index', methods=['POST','GET'])
def index():
    print request.headers
    return 'Hello'

@app.route('/login', methods=['POST','GET'])
def login():
    print request.headers
    uid, pw = base64.b64decode(request.headers['Authorization'].split(' ')[-1]).split(':')
    if users.get(uid)[0] == pw:
        return gen_token(uid)
    else:
        return 'error'

@app.route('/test', methods=['POST','GET'])
def test():
    token = request.args.get('token')
    #print "token:" +token
    if vertify_token(token) == 1:
        return 'data'
    else:
        print vertify_token(token)
        return 'error'


if __name__ == '__main__':
    app.run(debug=True)

```
app.run()函数中添加host参数后，可以外部访问192.168.0.88地址

**client端**代码如下：

```

import requests

#**第一段**获取token，将token加密后通过headers存储到users中
#r = requests.get('http://127.0.0.1:5000/login', auth=('magigo', '123456'))
#print r.text

#**第二段**验证token，获取token后，验证当前token与存储值是否相同。每次获取新的token都不同，#所以token值都不同
token ="bWFnaWdvOjAuNjMxNDU2NDIyNjMyOjE0NzI0NTA1MDAuMzQ="
r= requests.get('http://127.0.0.1:5000/test', params={'token':token})
print r.text
```
2.执行第一段代码，第二段代码进行注释，获取token，同时token也会添加到users中。
3.执行第二段代码，注释第一段代码时，需要更换token值，此时会返回token是否成功标识，若token成功则返回data，否则返回error

**4.遇到的问题**
4.1 哈哈，非常低级的问题，要先执行server.py开启服务后，再执行client.py才会有返回结果，如果提示有错误，先用浏览器打开，看能否get到信息
4.2 Authorization KeyError. 是因为headers中没有对应的字段。但是当时为什么会提示这个问题至今不懂，，俩个不同的环境，家里的操作环境提示KeyError,公司的办公环境就可以正确执行，所以应该是系统配置哪里出了问题
4.3 执行client.py的验证token后不能正确返回data值。如果有错可以一步一步打印信息查看每一步的返回值，当时返回error是因为没有先执行login函数，token未添加到users中，打印出日志便可以定位错误。

**再接再厉**

