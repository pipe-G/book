#Xadmin
##安装
* 安装Django的版本最好是2.0以下，因为2.0以上的对老版本的xadmin很多地方不兼容:

>django1.x含有django.core.urlresolversdjango.core.urlresolvers模块，但是在django2.x之后就没有此模块了，而改成了django.urls模块，所以用老版本的xadmin会有很多模块上的改变，还有furture模块的安装等还有models中对于级联更新操作，models.ForeignKey()，在django 2.x之前不用自己加上on_delete参数，但是如果django2.x之后不加的话，就会报错

1. 可以通过[github](https://github.com/sshwsfc/xadmin)下载，也可以通过pip安装,建议最好不要通过pip安装，后期不是太容易修改源码。
2. 根据github下载的requirement.txt下载所需要的模块。
3. settings中INSTALLED_APPS加上
    * 'xadmin',
    * 'crispy_forms'
4. 修改LANGUAGE_CODE，USE_TZ
	
		LANGUAGE_CODE = 'zh-hans'
		TIME_ZONE = 'Asia/Shanghai'
		USE_I18N = True
		USE_L10N = True
		#默认是Ture，时间是utc时间，由于我们要用本地时间，所用手动修改为false
		USE_TZ = False
5. 修改urls.py
	
		import xadmin
		urlpatterns = [
		    url(r'^xadmin/', xadmin.site.urls),
		]
6. 收集静态文件
		 
		 python manage.py collectstatic
## 使用
[文档参考一](https://www.cnblogs.com/wumingxiaoyao/p/6945449.html)

	[文档参考二](https://www.cnblogs.com/wumingxiaoyao/p/6945769.html)
	
 1. 生成的app都包括admin.py,django原有的管理后台，Xadmin需要adminx.py,可以在adminx中添加需要的表
 2. adminx.py,书写时尽量使用中括号[]，使用小括号（）的话 list_display（或者其他属性）需要添加models中全部字段，否则会出现错误。
	 	
		 	# Register your models here.
			import  xadmin
			from main.models import *
		
			class PeopleAdmin(object):
			    list_display=['name']    #显示字段
			    search_fields=['name','age']   # 添加搜索功能
			    list_filter=['age']           #添加过滤功能
			    exclude=['age'] #设置隐藏添加时的字段
    			list_editable=['name'] # 设置可以直接在页面上编辑的字段
			xadmin.site.register(People,PeopleAdmin) #将管理器注册
[Django——Xadmin中的功能](https://blog.csdn.net/lyysr/article/details/48735053)

[Django——xadmin使用要点](https://blog.csdn.net/qq_35037977/article/details/79217079)