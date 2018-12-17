#Django 
创建项目
django-admin startproject mysite
创建app
python manage.py startapp polls
##Django的源文件
>python -c "import django; print(django.__path__)"
##manage.py
>一个让你用各种方式管理 Django 项目的命令行工具。
**DJANGO_SETTINGS_MODULE（环境变量）** 让 Django 根据 mysite/settings.py 文件来设置 Python 包的导入路径
##__init__.py
一个空文件，告诉 Python 这个目录应该被认为是一个 Python 包
##settings.py
Django 项目的配置文件
##urls.py
Django 项目的 URL 声明，就像你网站的“目录”
##wsgi.py
作为你的项目的运行在 WSGI 兼容的Web服务器上的入口

#Djangoy运行命令
python manage.py runserver 
/ python manage.py runserver 0.0.0.0:8000(可以省略)


函数 include() 允许引用其它 URLconfs。每当 Django 遇到 :func：~django.urls.include 时，它会截断与此项匹配的 URL 的部分，并将剩余的字符串发送到 URLconf 以供进一步处理。
urlpatterns 中的
###path()：四个参数
>**1.**route：
route 是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 urlpatterns 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。
**2.**view：
当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 HttpRequest 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。稍后，我们会给出一个例子。
**3.**kwargs：
任意个关键字参数可以作为一个字典传递给目标视图函数
**4.**name：
为你的 URL 取名能使你在 Django 的任意地方唯一地引用它，尤其是在模板中。这个有用的特性允许你只改一个文件就能全局地修改某个 URL 模式

##数据库生成迁移文件
>python manage.py makemigrations

通过运行 makemigrations 命令，Django 会检测你对模型文件的修改（在这种情况下，你已经取得了新的），并且把修改的部分储存为一次 迁移。

##数据库完成迁移
>python manage.py migrate

migrate 命令检查 INSTALLED_APPS 设置，为其中的每个应用创建需要的数据表



>python manage.py sqlmigrate polls(app名字) 0001（migrations下的）

迁移命令会执行哪些 SQL 语句
>python manage.py check 

这个命令帮助你检查项目中的问题，并且在检查过程中不会对数据库进行任何操作。
 
>python manage.py shell
 
 交互式python命令行


> python manage.py createsuperuser
 
 创建一个能登录管理页面的用户

DjangoTemplates 在每个 	  INSTALLED_APPS 文件夹中寻找 "templates" 子目录

* render()
 「载入模板，填充上下文，再返回由它生成的 
HttpResponse 对象」是一个非常常用的操作流程
* get_object_or_404() 
  尝试用 get() 函数获取一个对象，如果不存在就抛出 Http404 错误也是一个普遍的流程
  
 * request.POST['']如果不存在，会爆出keyerror
  
 * HttpResponseRedirect 只接收一个参数：用户将要被重定向的 URL
  
 * reverse() 函数。这个函数避免了我们在视图函数中硬编码 URL :
   第一部分是指的是URL中的path
   reverse('polls:results', args=(question.id,)
   
##视图
>所有视图都继承自View类
>RedirectView用于简单的HTTP重定向
>TemplateView 扩展其他类呈现模板
url指向 方法as_view() 

	#some_app/views.py
	from django.views.generic import TemplateView
	
	class AboutView(TemplateView):
	    template_name = "about.html"
	    
	》# urls.py
	from django.urls import path
	from some_app.views import AboutView
	
	urlpatterns = [
	    path('about/', AboutView.as_view()),
	]    
##时间
区别在于时区是否开启
关闭时区(naive)
datetime.datetime.now()
开启时区(aware)
timezone.now()

## 自动化测试
>python manage.py test polls 将会寻找 polls 应用里的测试代码
它找到了 django.test.TestCase 的一个子类
它创建一个特殊的数据库供测试使用
它在类中寻找测试方法——以 test 开头的方法。

### 测试建议
>对于每个模型和视图都建立单独的 TestClass
每个测试方法只测试一个功能
给每个测试方法起个能描述其功能的名字


* django.contrib.staticfiles 存在的意义：它将各个应用的静态文件（和一些你指明的目录里的文件）统一收集起来

* Django 的**STATICFILES_FINDERS** 设置包
含了一系列的查找器，它们知道去哪里找到 static 文件。**AppDirectoriesFinder** 是默认查找器中的一个，它会在每个 INSTALLED_APPS 中指定的应用的子文件中寻找名称为 static 的特定文件夹
##后台页面管理
添加标题名字：fieldsets
样式：list_display  list_filter
##模板
>settings.py 中TEMPLATES中
>DIRS默认为空，可以设置模板的路径，引入模板

##Django数据库操作
[数据库](https://www.cnblogs.com/yangmv/p/5327477.html)
[数据库字段设置](https://blog.csdn.net/Great_Zhou/article/details/82560343)
（1）primary_key=True 主键
（2）default=None 默认值
（3）null=True  允许数据库为空，为True时，使用NULL来存储空值，
（4）blank=True 该字段允许为空
（5）**class Meta**
*abstract =True 声明模型为抽象类
此类将不用于创建任何数据库表，如果使用，其子类会把子段添加进去。
*

*proxy = True 声明模型为代理模型
可以添加新的方法，新的功能
*

*db_label=‘music_album’ 声明数据库的表名*

*verbose_name verbose_name = "pizza" 对象的（单数）可读名称，如果没有设置Django默认的格式为 CamelCase 变成 camel case.
*

*verbose_name_plural verbose_name_plural = "stories"
 对象的（复数）名称，如果没有设置Django默认的格式为verbose_name + "s".*

*unique_together=('cph','txzlx')
联合主键，参数是字段名
*

*设置外键的是子表*

	teacher = models.ForeignKey(Teacher, on_delete=models.CASCADE, default='')
	
	
*related_name
设置之后等同于 子表名称
+_set
作用可以通过主表，查询子表信息，例如:*

	teacher = models.ForeignKey
	(Teacher, related_name='student_teacher', on_delete=models.CASCADE, default='')
		
	tt=Teacher.objects.get(name='')
	tt.student_set.all() == tt.student_teacher.all()


###查
	models.UserInfo.objects.all()
	models.UserInfo.objects.all().values('user')    #只取user列
	models.UserInfo.objects.all().values_list('id','user')    #取出id和user列，并生成一个列表
	models.UserInfo.objects.get(id=1)
	models.UserInfo.objects.get(user='yangmv')

**get是用来获取一个对象的，如果需要获取满足条件的一些人，就要用到filter**

	Person.objects.filter
	(name="abc")  
	# 等于Person.objects.filter
	(name__exact="abc") 
	名称严格等于 "abc" 的人
###增
	models.UserInfo.objects.create
	(user='yangmv',pwd='123456')
或者
	 
	 obj = 	models.UserInfo
	 (user='yangmv',pwd='123456')
	 obj.save()
或者
	 
	dic = {'user':'yangmv',
	'pwd':'123456'}
	models.UserInfo.objects.create(**dic)

或者

	twz = Author()
	twz.name="WeizhongTu"
	twz.email="tuweizhong@163.com"
	twz.save()
或者

首先尝试获取，不存在就创建，可以防止重复
   
    Author.objects.get_or_create
    (name="WeizhongTu",  email="tuweizhong@163.com")
	返回值(object, True/False)

###删
	models.UserInfo.objects.filter
	(user='yangmv').delete()
###改

	models.UserInfo.objects.filter 
    (user='yangmv').update(pwd='520')

或者

	obj = models.UserInfo.objects.get(user='yangmv')
	obj.pwd = '520'
	obj.save()
##Django-log
Loggin一般由四部分组成：

* Loggers
* Handlers
* Filters
* Formatters

1.Loggers:

logger 是日志系统的入口，logger 可以配置 日志级别。日志级别描述了由该 logger 处理的消息的严重性。Python 定义了下面几种日志级别：
>DEBUG：排查故障时使用的低级别系统信息
>INFO：一般的系统信息
>WARNING：描述系统发生了一些小问题的信息
>ERROR：描述系统发生了大问题的信息
>CRITICAL：描述系统发生严重问题的信息


当 logger 确定了一条消息需要处理之后，会把它传给 Handler

2.Handlers

Handler 是决定如何处理 logger 中每一条消息的引擎。它描述特定的日志行为，比如把消息输出到屏幕、文件或网络 socket。

和 logger 一样，handler 也有日志级别的概念。如果一条日志记录的级别不匹配或者低于 handler 的日志级别，对应的消息会被 handler 忽略。

一个 logger 可以有多个 handler，每一个 handler 可以有不同的日志级别.

3.Filters

在日志记录从 logger 传到 handler 的过程中，使用 Filter 来做额外的控制
Filter 在 logger 和 handler 中都可以添加；多个 filter 可以链接起来使用，来做多重过滤操作

4.Formatters

日志记录最终是需要以文本来呈现的。Formatter 描述了文本的格式
如果LOGGING这个配置项中的disable_existing_loggers 被设置为True（默认就是Ture），那么默认配置中的logger全部被禁用
 
## Django 事务

（1）当 数据库的配置中ATOMIC_REQUESTS的值为True时，会把每个请求都封装成一个事务，但是这种方式在数据量比较大的时候，会出现比较差的效率，但是可以通过装饰器来阻止视图运行一个事务操作。

>non_ atomic _requests(using=None)

	from django.db import transaction
	
	@transaction.non_atomic_requests
	def my_view(request):
	    do_stuff()
	
	@transaction.non_atomic_requests(using='other')
	def my_other_view(request):
	    do_stuff_on_the_other_database()

(2)可以通过Django提供了的单一的API来控制数据库事务。

>atomic(using=None, savepoint=True)

原子性是由数据库的事务操作来界定的。 atomic允许我们在执行代码块时，在数据库层面提供原子性保证。 如果代码块成功完成， 相应的变化会被提交到数据库进行commit；如果执行期间遇到异常，则会将该段代码所涉及的所有更改回滚。

atomic块可以嵌套。 在下面的例子里，使用with语句，当一个内部块完成后，如果某个异常在外部块被抛出，内部块上的操作仍然可以回滚(前提是外部块也被atomic装饰过)。

atomic 被用作装饰器:
	
	from django.db import transaction
	
	@transaction.atomic
	def viewfunc(request):
	    # This code executes inside a transaction.
	    do_stuff()

atomic被用作 上下文管理器:

	from django.db import transaction
	
	def viewfunc(request):
	    # This code executes in autocommit mode (Django's default).
	    do_stuff()
	
	    with transaction.atomic():
	        # This code executes inside a transaction.
	        do_more_stuff()
	        
经过 atomic装饰的代码在一个 try/except 块内允许使用常见的完整性错误检测语法:

	from django.db import IntegrityError, transaction
	
	@transaction.atomic
	def viewfunc(request):
	    create_parent()
	
	    try:
	        with transaction.atomic():
	            generate_relationships()
	    except IntegrityError:
	        handle_exception()
	
	    add_children()


>在这个例子中，即使generate _ relationships() 
>违反完整性约束导致了数据库错误， 你仍可以进行add_children()的操作, 并且create _ parent()的变化仍然存在。注意，当 handle_exception()被触发时，在generate _ relationships()上的尝试操作已经被安全回滚，所以若有必要，这个异常的句柄也能够操作数据库。


