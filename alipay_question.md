#装饰器
在不该函数原来的执行方式，在新添一个新的功能
For example：
>**def wrapper1(func):
    def inner():
        print('wrapper1 ,before func')
        func()
        print('wrapper1 ,after func')
    return inner**

>**def wrapper2(func):
    def inner():
        print('wrapper2 ,before func')
        func()
        print('wrapper2 ,after func')
    return inner**

>**@wrapper2
@wrapper1
def f():
    print('in f')**

>**f()**

执行结果：
>wrapper2 ,before func
wrapper1 ,before func
in f
wrapper1 ,after func
wrapper2 ,after func**

https://www.cnblogs.com/Eva-J/articles/7194277.html
在**类视图**中使用为**函数视图准备的装饰器**时，不能直接添加装饰器，需要使用method_decorator将其转换为适用于**类视图方法**的装饰器。

from django.utils.decorators import method_decorator

@method_decorator(函数名称, name='dispatch'（这个是类中所有方法都添加装饰器，也可以写方法名字指定方法）)
#class
__init__()和__call__()的区别如下： 
1. __init__()的作用是初始化某个类的一个实例。 
2. __call__()的作用是使实例能够像函数一样被调用，同时不影响实例本身的生命周期（__call__()不影响一个实例的构造和析构）。但是__call__()可以用来改变实例的内部成员的值。
>class X(object):
    def __init__(self, a, b, range):
        self.a = a
        self.b = b
        self.range = range
    def __call__(self, a, b):
        self.a = a
        self.b = b
        print('__call__ with （{}, {}）'.format(self.a, self.b))
>> xInstance = X(1, 2, 3)
>> xInstance(1,2)
__call__ with (1, 2)

#models中class Meta 

1. abstract =True 
  声明模型为抽象类
2. db_label=‘music_album’
  声明数据库的表名
3. verbose_name 
    verbose_name = "pizza"
   对象的（单数）可读名称，如果没有设置Django默认的格式为 CamelCase 变成 camel case.
4. verbose_name_plural
   verbose_name_plural = "stories"
   对象的（复数）名称，如果没有设置Django默认的格式为verbose_name + "s".
# Vue
1. mac启动vue需要添加sudo+指令。
2. 执行启动命令需在vue项目目录下。
　npm run dev
3. 如果报出：This is probably not a problem with npm
可以使用npm install 或 cnpm install 
4.安装cnpm下载东西 
> sudo npm install -g cnpm –registry=https://registry.npm.taobao.org –verbose

#视图View
from django.views import View
默认视图接受的HTTP方法的名称列表
['get', 'post', 'put', 'patch', 'delete', 'head', 'options', 'trace']

# 前后端启动

# Requests
**1.** 发送网络请求
可以通过request.get(url)
或者不同的HTTP请求类型，需要使用不同的请求
以下分别是HTTP POST、PUT、DELETE、HEAD、OPTIONS:
request.post
request.put
request.delete
request.head
request.head
request.optinons
**2.** 传递URL参数
可以通过参数parm
requests.get("http://httpbin.org/get", **params=payload**)
url格式为：
requests.get("http://httpbin.org/get", params=payload)
属性encoding，改变编码格式
属性content，字节的形式访问
**3.**
Requests内置的JSON解码器
例如：
>r = requests.get('https://api.github.com/events')
> r.json()
[{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...
获取来自服务器的原始套接字响应
> r = requests.get('https://api.github.com/events', stream=True)
> r.raw
<requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
> r.raw.read(10)
'\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'

**4**
定制请求头
>url = 'https://api.github.com/some/endpoint'
> headers = {'user-agent': 'my-app/0.0.1'}
> r = requests.get(url, headers=headers)

所有的 header 值必须是 string、bytestring 或者 unicode
**5.**
发送一些编码为表单形式的数据——非常像一个 HTML 表单。要实现这个，只需简单地传递一个字典给 data 参数
>payload = {'key1': 'value1', 'key2': 'value2'}

>r = requests.post("http://httpbin.org/post", data=payload)
>print(r.text)
{
  ...
  "form": {
    "key2": "value2",
    "key1": "value1"
  },
  ...
}
**6**
POST一个多部分编码(Multipart-Encoded)的文件
requests.post(url, files=files)
可以显式地设置文件名，文件类型和请求头，发送作为文件来接收的字符串。
如果需要请求做成数据流，可以使用 requests-toolbelt
r = requests.get('http://httpbin.org/get')
响应状态码 ： r.status_code
状态码查询对象：r.status_code == requests.codes.ok
结果:True
抛出异常：
Response.raise_for_status() 
Cookie：Reponse.cookies['']
Cookie 的返回对象为 RequestsCookieJar，它的行为和字典类似，但接口更为完整，适合跨域名跨路径使用
**7.超时**
> requests 在经过以 timeout 参数设定的秒数时间之后停止等待响应。基本上所有的生产代码都应该使用这一参数
> requests.get('http://github.com', timeout=0.001)
timeout 仅对连接过程有效，与响应体的下载无关

# Alipay 第三方平台
一、不使用python SDK
1.URL拼接
注意：redirect_uri 与本次执行没有关系，填写什么可以，它是执行完毕之后，所要调转的界面，
拼接可以使用from requests import PreparedRequest
 PreparedRequest().prepare_url(url,parms)
拼接的url是支付宝的网关地址，同时注意单词的书写。
2、获取auth_code
注意:支付宝在成功登陆之后，会根据redirect_uri跳转回来，并携带auth_code.auth_code.
3、 通过auth_code换取access_token和user_id

通过alipay.system.oauth.token接口的使用，注意一个参数 **code**，缺少，会导致授权码无效。sign的签名可以通过代码书写，规则[自行实现签名](https://docs.open.alipay.com/291/106118)
然后通过requests.get(url, params=**)发送过去，返回之后可以变成json：response.json()
4、 通过access_token，调用api,得到信息。
# Alipay 支付
* 使用接口：alipay.trade.wap.pay
* 接口含有公共参数和请求参数，
* 请求参数都放在biz_content
* 请求参数必选中有一个参数（seller_id）可以不用书写，含有默认值。
* 先对url进行拼凑然后跳转，再获取信息。
如果直接使用requests，会报出错误：
json.decoder.JSONDecodeError: Expecting value: line 1 column 1 (char 0)
# 微信
1. json.loads(json_str) json字符串转换成字典
 json.dumps(dict) 字典转换成json字符串 
 json的禁止转义unicode ensure_ascii=False,并转换成utf-8格式
 例子：
 
> json.dumps(parms, ensure_ascii=False).encode(encoding='UTF-8'))

2. 微信对url 进行验证，返回验证信息
转换hashcode，需这样
str=list[0]+list[1]+list[2]
sha1 = hashlib.sha1()          sha1.update(str.encode('utf-8'))
hashcode = sha1.hexdigest()
3.获取openid，通过post传递。
（1）需要解决Django 中针对基于类的视图添加 csrf_exempt
* 在类的 dispatch 方法上使用 @csrf_exempt
* 在 urls.py 中配置：

> urlpatterns = [url(r'^myview/$', csrf_exempt(views.MyView.as_view()), name='myview'),
]

(2)数据传递过来，request.POST 获取不到
使用request.META：
> request.META.get('QUERY_STRING').split('&')[3].split('=')[1]
#isinstance() 函数
 判断一个对象是否是一个已知的类型
 isinstance() 与 type() 区别：

type() 不会认为子类是一种父类类型，不考虑继承关系。

isinstance() 会认为子类是一种父类类型，**考虑继承关系。
**
如果要判断两个类型是否相同推荐使用 isinstance()。
>a = 2
> isinstance (a,int)
True
> isinstance (a,str)
False
> isinstance (a,(str,int,list))    # 是元组中的一个返回 True
True

# wechatpy
[函数网址](https://wechatpy.readthedocs.io/zh_CN/master/install.html)
1.微信xml信息保存在msg中
 msg=parse_message(request.body)
2.
>* TextReply（文本）
参数:content(保存到文本信息)
message(保存信息体)
reply = TextReply(content='text reply', message=msg)
或者
reply = TextReply(message=msg)
reply.content = 'text reply'

* ImageReply(图像)
参数:media_id(存放图片Id，可以查找图片)
message(保存信息体)
* VoiceReply(语音)
参数:media_id(存放语音Id，可以查找语音)
message(保存信息体)
* VideoReply(视频)
参数:media_id(存放视频Id，可以查找视频)
message(保存信息体)
3. 转换成xml
xml = reply.render()