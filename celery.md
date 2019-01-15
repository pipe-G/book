#Celery
1.定时任务
2.异步任务队列
###中间件redis
	pip install -U "celery[redis]"
	
	pip install -U "celery[redis]"  
	安装celery

同时安装redis组件作为中间人

	brew install redis  安装redis服务器
	brew services start redis   启动redis服务器
	ps aux|grep redis 查看redis服务器运行状态
	
启动celery 服务器
	
	celery -A alipay_zzjj worker --loglevel=info  
加载tasks文件，这个文件有两个作用：

    （1）初始化celery对象
    （2）装饰需要异步的函数
然后就可以使用异步函数了，可以在命令行看到执行的结果。
[耗时任务](https://www.jianshu.com/p/6f8576a37a3e)

	celery -A alipay_zzjj beat -l info
	 加载beat程序，使其能够执行定时任务。

配置定时任务的步骤：（有两种办法，现在先说简单的一种）

1.配置 app.conf.beat_schedule 。
    以下面这个为例。
	    
    app.conf.beat_schedule = {
    'perminute': {              # 任务名，可自定义
        'task': 'tasks.test',    # 需要加载的异步函数
        'schedule': crontab(),  # 时间控制，这个是重中之重，见2
        # 'schedule': crontab(hour=1,minute=0),
        'args': (1, 2)          # 函数位置参数
        'kwargs': {}            # 函数关键字参数
        'options':{}            # 选项，apply_async()的各种参数设置。
    }
	}
2.时间控制

    （1）直接设置秒数，例如10就代表10秒，1 * 60 就代表1分钟。
    （2）通过datetime设置时间间隔。
        有时直接设置秒数不方便，需要通过计算得到具体秒数。例如，1小时15分钟40秒 = 1*60*60 + 15*60 + 40。这种情况可读性也不高。
        可以采用datetime设置，datetime.timedelta(hours=1, minutes=15, seconds=40)
    （3）crontab表达式，设置具体的时间点。
 [参考网址](http://yshblog.com/blog/164)
>5个常用参数：
	 
	minute：分钟，范围0-59；
	hour：小时，范围0-23；
	day_of_week：星期几，范围0-6。以星期天为开始，即0为星期天。这个星期几还可以使用英文缩写表示，例如“sun”表示星期天；
	day_of_month：每月第几号，范围1-31；
	month_of_year：月份，范围1-12。

其实这个配置已经够用了，还有第二种方法，仅做了解。
   
    @app.on_after_configure.connect
    def setup_periodic_tasks(sender, **kwargs):
        # Calls test('hello') every 10 seconds.
        sender.add_periodic_task(10.0, test.s('hello'), name='add every 10')
        # 第一个参数是时间控制，第二个是异步函数及参数，第三个不深究。

        # Calls test('world') every 30 seconds
        sender.add_periodic_task(30.0, test.s('world'), expires=10)

        # Executes every Monday morning at 7:30 a.m.
        sender.add_periodic_task(
            crontab(hour=7, minute=30, day_of_week=1),
            test.s('Happy Mondays!'),
        )

    @app.task
    def test(arg):
        print(arg)
>on_after_configure装饰器和add_periodic_task可以把这些任务添加到beat_schedule中。
它的效果与上面的配置一样。

##Django Settings
	# 消息中间件（使用redis），消息代理，用于发布者传递消息给消费者
	CELERY_BROKER_URL = 'redis://127.0.0.1:6379'
	#
	# BROKER_TRANSPORT = 'redis'
	# 消息结果返回中间件（使用redis），用于存储任务执行结果
	CELERY_RESULT_BACKEND= 'redis://127.0.0.1:6379'	
	# 允许的内容类型
	CELERY_ACCEPT_CONTENT = ['json']
	# 任务的序列化方式
	CELERY_TASK_SERIALIZER = 'json'
	# 任务结果的序列化方式
	CELERY_RESULT_SERIALIZER = 'json'
	# celery时区，定时任务使用
	CELERY_TIMEZONE = 'Asia/Shanghai'）
##Flower(celery监控工具)
[flower](https://flower-docs-cn.readthedocs.io/zh/latest/)
	 
	 celery flower --broker=redis://127.0.0.1:6379  --address=127.0.0.1 --port=5555