# Sentry
## 安装
####一、老版SDK

1.下载Raven
		
	--upgrade（批量更新）
	pip install raven --upgrade

2.settings中INSTALLED_APPS添加

	INSTALLED_APPS = (
    'raven.contrib.django.raven_compat',
	)

3.在settings中使用RAVEN_CONFIG配置客户端的设置

	import os
	import raven
	
	RAVEN_CONFIG = {
	    'dsn': 'https://<key>:<secret>@sentry.io/<project>',
	    # If you are using git, you can also automatically configure the
	    # release based on the git info.
	    'release': raven.fetch_git_sha(os.path.abspath(os.pardir)),
	}
4.测试配置完成
	
	python manage.py raven test
####二、新版SDK
 1.安装serntry-sdk:
	
 	pip install --upgrade sentry-sdk==0.6.4
 2.配置sdk,settings文件中添加。
 
	import sentry_sdk
	from sentry_sdk.integrations.django import DjangoIntegration
	
	sentry_sdk.init(
	    dsn="https://<key>@sentry.io/<project>",
	    integrations=[DjangoIntegration()]
			)
## 初用
####一、Django老版SDK
>可以通过导入包，使用

	from raven.contrib.django.raven_compat.models import client
	
	client.captureException()
--
示例如下：
	 
	try:
		1/0
	except Exception as e:
		print(type(e).__name__)
		client.captureException()
		client.captureMessage('somtheing is wrong')

####二、Django新版SDK
>导入包，使用

	from sentry_sdk import   
	capture_exception,capture_message
--
示例如下：
    
    from sentry_sdk import
     	capture_exception,capture_message
	try:
	    1/0
    except Exception as e:
	    capture_exception(e)
	    capture_message('错误')
##Django And Sentry(老版SDK)
一、