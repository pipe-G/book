#PostgREST
##安装
可以使用docker对postgREST进行测验

	sudo docker run --name tutorial -p 5432:5432 \
	 -e POSTGRES_PASSWORD=mysecretpassword \
	 -d postgres
从[官网](https://github.com/PostgREST/postgrest/releases/latest)下载
>注意：
>需要PostgreSQL 9.3或更高版本，如果使用安全功能，建议使用9.5以上。

下载完毕，可以尝试运行：
			
	./postgrest
>注意：PostgREST要求在您的系统上安装libpq，如果没有库，将会出现错误，例如：error while loading shared libraries: libpq.so.5。
>>Fedora, CentOS, or Red Hat:
>>>sudo yum install postgresql-libs

>>mac:
>>>brew install postgresql

##创建数据库
**1.**连接到容器内的SQL控制台
	
	sudo docker exec -it tutorial psql -U postgres
**2.**为数据库创建一个命名模式（schema），一个数据库包含一个或者多个命名模式，模式中包含表，对象等，同一个对象名可以在不同的模式中，而不引起冲突。一般创建数据库，默认的模式为public。
>模式的特点：
	
	1).允许多个用户使用一个数据库而不会干扰其它用户。
    2). 把数据库对象组织成逻辑组，让它们更便于管理。
    3). 第三方的应用可以放在不同的模式中，这样它们就不会和其它对象的名字冲突。
>模式的语法：
	
	1.创建模式：
	CREATE SCHEMA name;
	2.模式中创建表
	CREATE TABLE name.mytable (
	...
	);	
	3.删除模式
	（1）如果模式为空，其中的所有对象都已删除
		DROP SCHEMA myschema;
	（2）删除包含所有包含对象的模式
		DROP SCHEMA myschema CASCADE;
--
	create schema api;
>创建表格
	
	create table api.todos (
	  id serial primary key,
	  done boolean not null default false,
	  task text not null,
	  due timestamptz
	)
>插入数据
	
	insert into api.todos (task) values
    ('finish tutorial 0'), ('pat self on back');
>将角色用于匿名Web请求。当请求进入时，PostgREST将在数据库中切换到此角色以运行查询
	
	create role web_anon nologin;
	权限：
	grant web_anon to postgres;
	
	grant usage on schema api to web_anon;
	grant select on api.todos to web_anon;
[权限参数解释](https://www.postgresql.org/docs/10/sql-grant.html)
##运行PostgREST
###无身份验证
**1.**创建 .conf 文件

	# postgrest.conf
	
	# The standard connection URI format, documented at
	# https://www.postgresql.org/docs/current/static/libpq-connect.html#AEN45347
	db-uri       = "postgres://user:pass@host:5432/dbname"
	
	# The name of which database schema to expose to REST clients
	db-schema    = "api"
	
	# The database role to use when no client authentication is provided.
	# Can (and probably should) differ from user in db-uri
	db-anon-role = "anon"
[参数详解](http://postgrest.org/en/stable/install.html)

--
	db-uri = "postgres://postgres:mysecretpassword@localhost/postgres"
	db-schema = "api"
	db-anon-role = "web_anon"

**2.**运行 .conf 文件

	./postgrest tutorial.conf
--
结果：
	
	Listening on port 3000
	Attempting to connect to the database...
	Connection successful
**3.** 根据url进行跳转获取数据
可以使用postman，或者使用curl
	
	curl http://localhost:3000/todos
###身份验证
**1.**创建用户
	
	create role todo_user nologin;
	grant todo_user to postgres;
	
	grant usage on schema api to todo_user;
	grant all on api.todos to todo_user;
	grant usage, select on sequence api.todos_id_seq to todo_user;
**2.**生成密码
>注意：
>OpenSSL toolkit 提个一个简单的方式来生成安全的密码。如果你有安装，运行

	openssl rand -base64 32
**3.**添加密码
	
	# add this line to tutorial.conf

	jwt-secret = "<the password you created>"
**4.**生成token
>通常自己的代码在数据库或其他服务器中将创建并签署身份验证 token,下面是手动生成的。打开[jwt.io](https://jwt.io/#debugger-io)

PAYLOAD:DATA


{
  "role":"todo_user"
}

VERIFY SIGNATURE

HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  '生成的密码'

) secret base64 encoded
**5.**请求

	export TOKEN="<paste token here>"
	
	curl http://localhost:3000/todos -X POST \
	     -H "Authorization: Bearer $TOKEN"   \
	     -H "Content-Type: application/json" \
	     -d '{"task": "learn how to auth"}'
	更换整体使用put，更新单个资源使用patch
	curl http://localhost:3000/todos -X PATCH \
     -H "Authorization: Bearer $TOKEN"    \
     -H "Content-Type: application/json"  \
     -d '{"done": true}'

###令牌时效性
**1.**计算时间
可以在psql中运行:
	
	select extract(epoch from now() + '5 minutes'::interval) :: integer;
**2.**修改jwt.io
	{
  "role": "todo_user",
  "exp": 时间
}
**3.**请求
	
	export NEW_TOKEN="<paste new token>"
	尝试在到期时间之前和之后以curl发出此请求：

	curl http://localhost:3000/todos \
	     -H "Authorization: Bearer $NEW_TOKEN"
	到期后，API返回HTTP 401 Unauthorized：
	
	{"message":"JWT expired"}。
###撤销
**1.**需要一种方法来区别于其他令牌。让我们添加一个自定义email声明，该声明与发出令牌的客户端的电子邮件相匹配

	{
	  "role": "todo_user",
	  "email": "disgruntled@mycompany.com"
	}
>保存环境变量
	
	export WAYWARD_TOKEN="<paste new token>"
	
>创建一个新架构并添加该函数
	
	create schema auth;
	grant usage on schema auth to web_anon, todo_user;
	
	create or replace function auth.check_token() returns void
	  language plpgsql
	  as $$
	begin
	  if current_setting('request.jwt.claim.email', true) =
	     'disgruntled@mycompany.com' then
	    raise insufficient_privilege
	      using hint = 'Nope, we are on to you';
	  end if;
	end
	$$;
>更新tutorial.conf并指定新功能
	
	# add this line to tutorial.conf

	pre-request = "auth.check_token"
	
>结果

	# this request still works

	curl http://localhost:3000/todos \
	     -H "Authorization: Bearer $TOKEN"
	
	# this one is rejected
	
	curl http://localhost:3000/todos \
	     -H "Authorization: Bearer $WAYWARD_TOKEN"

##PostgREST API
一、**GET**
    
    rep="http://192.168.1.150:3000"
    url=rep+'/wx_test'
    查询所有
    response=requests.get(rep+'/wx_test').json()
    查询年龄小于20的
    params={
        'age':'lt.20'
    }
    response=requests.get(rep+'/wx_test',params=params)

    查询年龄等于2，姓名为11pipe
    params={
        'age':'eq.2',
        'tname':'eq.11pipe'
    }
    response=requests.get(rep+'/wx_test',params=params)
    查询年龄等于2或者10
    params={
        'or':'(age.eq.2,age.eq.10)'
    }
    response=requests.get(url=url,params=params)
    查询年龄大于等于1，小于等于10
    params={
        'and':'(age.gte.1,age.lte.10)'
    }
    response=requests.get(url=url,params=params)
    
    查询年龄在几个值中
    params={
        'age':'in.(10,2,19)'
    }
    response = requests.get(url=url,params=params)

    查询年龄，使用like
    params={
        'age':'like.*2*'
    }
    response =requests.get(url=url,params=params)
    
    #查询tname，查询出需要的列名
    params={
        'select':'tname',
        'age':'like.*10*'
    }
    response=requests.get(url=url,params=params)
    
    #查询，设置别名,形式为 别名+冒号:+列名
    params={
        'select':'tt:tname',
        'age':'like.*10*'
    }
    response=requests.get(url=url,params=params)
 对于json和jsonb数据可以通过->或者->>进行查找[更多字符含义](https://www.postgresql.org/docs/9.5/functions-json.html)

 [其他参数详解](http://postgrest.org/en/stable/api.html)
 
两表关联可以使用外键约束。
	
	GET /films?select=title,director:directors(id,last_name) HTTP/1.1
对于比较复杂的可以创建视图，和创建函数。格式为：

	CREATE VIEW fresh_stories AS
	SELECT *
	  FROM stories
	 WHERE pinned = true
	    OR published > now() - interval '1 day'
	ORDER BY pinned DESC, published DESC;
--
	CREATE FUNCTION full_name(people) RETURNS 
	text AS $$
    SELECT $1.fname || ' ' || $1.lname;
    $$ LANGUAGE SQL;

二、**POST**
需要把参数都写上去，否则会添加不进去，自增id一直是在增加的，但是数据库中没有数据。
	    
    #post,增加一列
    params = {
        'tname': 'uio',
        'age':'87'
    }
    response = requests.post(url=url,data=params)

三、**DELETE**
可根据需要删除那些，增添参数。
	
	requests.delete(url)
四、**PUT、PATCH**

patch,修改数据
   
    params={
        'age':'109'
    }
    response=requests.patch(url+'?id=eq.100',data=params)

put,修改数据,必须指定所有的列，包括主键列。（但是没有试验成功。。。。）

    params={
        'id':100,
        'tname':'ssss',
        'age':'120'
    }
    response=requests.put(url+'?id=eq.100',data=params)
 	
 ## 认证