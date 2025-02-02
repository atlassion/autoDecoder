# autoDecoder
想维护成一个有很多用例、接口的项目，希望各位师傅有加解密之类的需求可以一起沟通，完善本项目。

目前已有的例子如下：

1. [ldap的md5加密](https://github.com/f0ng/autoDecoder/blob/main/autoDecoder%E4%BE%8B%E5%AD%90/%E7%99%BB%E5%BD%95%E5%8F%A3%E7%88%86%E7%A0%B4%E4%B9%8Bldap%E7%9A%84md5%E5%8A%A0%E5%AF%86.md)——使用python的flask框架，配合`autoDecoder`插件、`captcha-killer-modified`插件爆破

2. [流量waf绕过](https://github.com/f0ng/autoDecoder/blob/main/autoDecoder%E4%BE%8B%E5%AD%90/%E7%BB%95%E8%BF%87%E6%B5%81%E9%87%8Fwaf/%E7%BB%95%E8%BF%87%E6%B5%81%E9%87%8Fwaf.md)——使用python的flask框架，绕过waf对webshell的流量通信的阻断

3. [sql注入绕过](https://github.com/f0ng/autoDecoder/blob/main/autoDecoder%E4%BE%8B%E5%AD%90/sql%E6%B3%A8%E5%85%A5%E7%BB%95%E8%BF%87%E4%B9%8Bsqlmap%E7%9A%84%E6%95%B0%E6%8D%AE%E5%8C%85%E6%8D%A2%E8%A1%8C%E9%97%AE%E9%A2%98.md)——使用python的flask框架，解决sqlmap的数据包换行问题

4. [sqlmap的osshell遇到中文字符情况](https://github.com/f0ng/autoDecoder/blob/main/autoDecoder%E4%BE%8B%E5%AD%90/sqlmap%E7%9A%84osshell%E9%81%87%E5%88%B0%E4%B8%AD%E6%96%87%E5%AD%97%E7%AC%A6%E6%83%85%E5%86%B5.md)——使用python的flask框架，解决osshell中的出现中文目录无法正常执行命令的通病

5. [SM4加密](https://github.com/f0ng/autoDecoder/blob/main/autoDecoder%E4%BE%8B%E5%AD%90/sm4%E5%8A%A0%E5%AF%86/sm4%E5%8A%A0%E5%AF%86.md)——使用nodejs的http接口，解决SM4加解密
 
6. [3DES加密](https://github.com/f0ng/autoDecoder/blob/main/autoDecoder%E4%BE%8B%E5%AD%90/3DES%E5%8A%A0%E5%AF%86/3DES%E5%8A%A0%E5%AF%86.md)——使用python的flask框架，解决3DES/CBC加解密

7. [AES加密](https://github.com/f0ng/autoDecoder/blob/main/autoDecoder%E4%BE%8B%E5%AD%90/AES%E5%8A%A0%E5%AF%86/AES%E5%8A%A0%E5%AF%86.md)——使用nodejs的http接口，解决AES/ECB加解密*另含有特殊关键字加解密处理方式*

8. [JSON嵌套加密](https://github.com/f0ng/autoDecoder/blob/main/autoDecoder%E4%BE%8B%E5%AD%90/JSON%E5%B5%8C%E5%A5%97%E5%8A%A0%E5%AF%86/JSON%E5%B5%8C%E5%A5%97%E5%8A%A0%E5%AF%86.md)——使用python的flask框架，解决json数据中嵌套一层base64编码的json加密数据的AES/ECB加解密

## 2022.5.18 更新0.18

- 增加`3DES加密`、 `AES加密`、 `JSON嵌套加密`两个例子

- 在明文发出请求的时候，如果响应包选中了默认选项卡，则无影响；但是当响应包选中了扩展选项卡，还会进行解密，导致请求包内的响应体报错，`0.17beta1`修复该问题：明文发出请求，响应包只有明文。
- 增加密文关键字，出现该关键字则不进行加密，具体可以查看例json嵌套加密
## 2022.5.15 更新0.17
- 优化Desede(3DES)加密处理问题，3DES加密的密钥为24位，当输入超过24位将会报密钥长度错误，处理方式为对密钥长度进行了截取

## 2022.5.11 更新 0.16
1. 增加响应头处理，传入参数同样为`dataHeaders`
```
# -*- coding:utf-8 -*-
# author:f0ngf0ng

from flask import Flask,Response,request
from pyDes import *
import base64
app = Flask(__name__)

@app.route('/encode',methods=["POST"])
def encrypt():
    body = request.form.get('dataBody')  # 获取  post 参数 必需
    headers = request.form.get('dataHeaders')  # 获取  post 参数  可选

    if headers != None: # 开启了请求头加密
        headers = headers + "aaaa:bbbb\r\n"
        headers = headers + "f0ng:test"
        print(headers + "\r\n\r\n\r\n\r\n" + body)
        return headers + "\r\n\r\n\r\n\r\n" + body # 返回值为固定格式，不可更改

    return  body

@app.route('/decode',methods=["POST"]) # 不解密
def decrypt():
    body = request.form.get('dataBody')  # 获取  post 参数 必需
    headers = request.form.get('dataHeaders')  # 获取  post 参数  可选
    if headers != None: # 开启了响应头加密
        print(headers + "\r\n\r\n\r\n\r\n" + body)
        headers = headers + "yyyy:zzzz\r\n"
        headers = headers + "f0ng:onlysecurity"
        return headers + "\r\n\r\n\r\n\r\n" + body # 返回值为固定格式，不可更改

    return body

if __name__ == '__main__':
    app.debug = True # 设置调试模式，生产模式的时候要关掉debug
    app.run(host="0.0.0.0",port="8888")
```

原始请求响应

<img width="380" alt="image" src="https://user-images.githubusercontent.com/48286013/167889115-6b4a45ac-0acf-4220-8263-d8497c446a78.png">


经过autoDecoder处理后的响应

<img width="380" alt="image" src="https://user-images.githubusercontent.com/48286013/167889126-6f3d57cd-9ec9-4796-a442-a730b24865ac.png">


2. 修复当请求体为空时候的报错
3. 修复当关键词置空时造成多出一个换行符的问题
## 2022.5.7 更新 0.15

* 优化设置域名处的端口问题，两种模式：

①只输入域名，匹配域名与任意端口号
  
<img width="330" alt="image" src="https://user-images.githubusercontent.com/48286013/167258838-6a10e777-5253-4b21-be26-80acf7343592.png">
  
匹配所有`www.baidu.com:端口号`，如`www.baidu.com:8080`、`www.baidu.com:8088`
  
②输入域名与端口号，匹配唯一域名与端口号host
  
<img width="354" alt="image" src="https://user-images.githubusercontent.com/48286013/167258977-a897288a-61b0-4838-ae2c-3d8bb945925b.png">
  
  只匹配`www.baidu.com:8080`



## 2022.4.26更新 0.14

1. 增加对整个请求包的处理，具体怎么修改，根据个人需要不同进行自定义了，模板文件为`flasktestheader.py`

配置如下：

<img src="https://user-images.githubusercontent.com/48286013/165212779-88601efd-d11d-44ff-bd2a-74fc8efee915.png" width="700" />

原始请求包，捕捉整个请求如下，在请求包添加额外的请求头`aaaa:bbbb`、`f0ng:test`

<img src="https://user-images.githubusercontent.com/48286013/165212786-17579322-9c9d-411f-964e-931608184f72.png" width="700" />

实际请求包

<img src="https://user-images.githubusercontent.com/48286013/165212793-b267a7ea-b965-4ed8-80f7-3bf1ca428fa8.png" width="700" />

2. 增加自定义设置明文关键字，当请求体中出现了相应的关键字则不对数据包进行处理，取`contains`进行判断
## 2022.4.22更新 0.13

1. 对于`\r\n`的请求包处理不够完善，0.13版本修复该问题

2. 增加案例1`登录口爆破之ldap的md5加密`，案例2`sql注入绕过之sqlmap的数据包换行问题`，方便更好使用工具进行渗透测试
## 0x01 背景
- 当数据包里都是密文，我们无从下手；就算是获得了加解密的一些关键信息，能解密出来，但是每个数据包我们都需要慢慢解密，请求包需要解密，响应包也需要解密，比较麻烦
- 其实取auto这个名字并不是真正的auto，加解密算法还是需要自己去逆出来的，只是相对于数据包里的密文来说，可以算是半自动

## 0x02 优点
- 明文传，明文响应；密文传，密文响应，不影响原本通讯包的基础上，增加一个bp扩展页面查看明文信息
- 自定义加解密的接口，当存在复杂数据加密的时候，可以自行编写python代码对接口进行加解密， 自定义需要加解密域名，即开即用

## 0x03 插件的加解密方式
- 直接通过插件自带的算法去加解密数据包(较为简单，仅支持部分AES、DES、DESede加密)
- 通过python的flask接口去编写加解密数据包的api（不一定是flask框架，也可以起其他框架，只需要接口地址正确且加解密流程正确即可）

## 0x04 文件介绍:
- `users.sql` 为测试所用数据库

- `testsql.php` 为加密请求数据、加密响应数据且存在注入的页面

- `flasktest.py` 为测试文件编写的Python flask加解密接口。

详细举例可至公众号查看https://mp.weixin.qq.com/s/B-lBbVpJsPdCp1pjz2Rxdg

>## 通过自带加解密算法进行解密
![通过自带加解密算法进行解密](https://user-images.githubusercontent.com/48286013/160236779-9848060d-94ff-493d-ace5-44aa85e6e444.gif)

>## 通过flask接口进行解密
![通过flask接口进行解密](https://user-images.githubusercontent.com/48286013/160237163-cd57267e-7e11-49cb-8ec5-41a896f917bc.gif)

>## sqlmap进行加解密
![sqlmap进行加解密](https://user-images.githubusercontent.com/48286013/160238771-dd33b0ef-1007-47a0-9659-c8a20ed6d89e.gif)


