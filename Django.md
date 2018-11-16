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

》 #some_app/views.py
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