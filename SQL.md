#SQL
>SQL(Structure Query Language)语言是数据库的核心语言。

>SQL语言共分为四大类：数据定义语言DDL，数据操纵语言DML，数据查询语言DQL，数据控制语言DCL。

>1. 数据定义语言DDL
数据定义语言DDL用来创建数据库中的各种对象-----表、视图、索引、同义词、聚簇等如：
CREATE TABLE/VIEW/INDEX/SYN/CLUSTER
DDL操作是隐性提交的！不能rollback 

>2 .数据操纵语言DML
数据操纵语言DML主要有三种形式：
>>1) 插入：INSERT

>>2) 更新：UPDATE

>>3) 删除：DELETE

>3. 数据查询语言DQL
数据查询语言DQL基本结构是由SELECT子句，FROM子句，WHERE子句组成的查询块：

>>SELECT <字段名表>

>>FROM <表或视图名>

>>WHERE <查询条件>

>4. 数据控制语言DCL
数据控制语言DCL用来授予或回收访问数据库的某种特权，并控制数据库操纵事务发生的时间及效果，对数据库实行监视等。如：

>>1) GRANT：授权。

>>2) ROLLBACK [WORK] TO [SAVEPOINT]：回退到某一点。
回滚---ROLLBACK
回滚命令使数据库状态回到上次最后提交的状态。其格式为：



####in,exists
	
	select * from cd.facilities where facid in(1,5) 
--
	select * from cd.members mems where 
	not exists (select * from cd.bookings 
	where memid = mems.memid);
	
####case when
>case when ... then ...

>when ... then ...

>else ...  end

OR
>case columname when ... then ...

>when ... then ...

>else ...  end
	
	case sex
	  when '1' then '男'
	  when '2' then '女’
	  else '其他' end
--
	select name,
	(case when monthlymaintenance >100 then 'expensive'
	when monthlymaintenance <100 then 'cheap'
	else '' end) as cast 
	from cd.facilities
####时间大小
1. 大于，小于

	    select memid,surname,firstname,joindate 
	    from cd.members where joindate   
	    >=('2012-09-01')
2. between and

		select * from cd.members where joindate   
	    between '2012-09-01' and '2012-09-10'

3. datediff函数先求出两个时间差，在做正负判断(一般不推荐使用)
			
		select * from cd.members where 
		datediff(joindate,'2012-09-01') >0
		and datediff(joindate,'2012-09-30') <0
  
#### order by（排序），limit （条数）

		select distinct(surname) 
		from cd.members order by 
		surname limit 10

#### UNION

UNION 操作符用于合并两个或多个 SELECT 语句的结果集。

请注意，UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同。

如果允许重复的值，请使用 UNION ALL

UNION ALL 命令和 UNION 命令几乎是等效的，不过 UNION ALL 命令会列出所有的值。
	
	SELECT column_name(s) FROM table_name1
	UNION
	SELECT column_name(s) FROM table_name2
	
	SELECT column_name(s) FROM table_name1
	UNION ALL
	SELECT column_name(s) FROM table_name2
	
#### MAX

	select MAX(joindate) as latest from cd.members 
	
#### 子查询

	select firstname,surname,joindate from cd.members 
	where joindate=(select max(joindate) from cd.members)
--
	select distinct 
	a.firstname||' '||a.surname as member,
	(select  
	b.firstname||' '||b.surname 
	as recommender from 
	cd.members as b 
	where a.recommendedby=b.memid)
	from cd.members as a
	order by member
	
#### inner join 
	
	select a.starttime as start ,b.name  
	from cd.bookings as a inner join 
	cd.facilities as b 
	on a.facid=b.facid 
	where b.name like 'Tennis%' and
	a.starttime >='2012-09-21' and 
	a.starttime < '2012-09-22'
	order by a.starttime;

#### left join
	
	select a.firstname as memfname ,a.surname as memsname,
	b.firstname as recfname ,b.surname as recsname
	from cd.members as a left join 
	cd.members as b on b.memid=a.recommendedby
	order by a.surname,a.firstname
#### right join
	
#### 字符串拼接
	
	select distinct mems.firstname 
	|| ' ' || 	mems.surname as member, 
	facs.name as facility
	from cd.members mems
			inner join cd.bookings bks
				on mems.memid = bks.memid
			inner join cd.facilities facs
				on bks.facid = facs.facid
		where
			bks.facid in (0,1)
	order by member          
	
#### EXTRACT 
EXTRACT() 函数用于返回日期/时间的单独部分，比如年、月、日、小时、分钟等等。
YEAR-年，MONTH-月，DAY-日，HOUR-小时,MINUTE-分钟

starttime——字段名

	select facid,extract(month FROM starttime) AS month,sum(slots) 
	from cd.bookings where EXTRACT(YEAR FROM starttime) ='2012'
	group by month,facid
	order by facid, month
	
#### generate_series(postgresql)
PostgreSQL 中有一个很有用处的内置函数generate_series，可以按不同的规则产生一系列的填充数据。

有三种用法，这里只介绍一种

1. generate_series(start, stop) 
生成一个数值序列，从start 到 stop，依次递加

####四舍五入(postgresql)
四舍五入函数：ROUND(x)、ROUND(x,y)
		
	SELECT ROUND(3.14), ROUND(-3.14), ROUND(2.56);
其返回值如下：

 round | round | round 
-------+-------+-------
     3 |    -3 |     3


ROUND(x,y)会返回对x进行四舍五入计算，其保留小数位为y。如果y是负数，则会保留x到小数点左边的y位。

例如，对3.14进行1位、0位和-1位四舍五入运算：

	SELECT ROUND(3.14,1), ROUND(3.14,0), ROUND(3.14,-1);
其返回值如下：

 round | round | round 
-------+-------+-------
   3.1 |     3 |     0

####随机数(postgresql)
random()函数返回0到1之间的随机数。以下语句返回0到1之间的随机数。
SELECT random();

random |
-------+
 0.867320362944156|
####having
与SQL Server和MySQL等其他RDBMS(系数据库管理系统)不同
postgresql 不支持在having中放入列名，如:

	select facs.name, sum(case 
			when memid = 0 then slots * facs.guestcost
			else slots * membercost
		end) as revenue
		from cd.bookings bks
		inner join cd.facilities facs
			on bks.facid = facs.facid
		group by facs.name
		having revenue < 1000
	order by revenue;

>会出现错误：
>>ERROR: column "revenue" does not exist. 

只能这样书写，或者使用子查询

	select facs.name, sum(case 
			when memid = 0 then slots * facs.guestcost
			else slots * membercost
		end) as revenue
		from cd.bookings bks
		inner join cd.facilities facs
			on bks.facid = facs.facid
		group by facs.name
		having sum(case 
			when memid = 0 then slots * facs.guestcost
			else slots * membercost
		end) < 1000
	order by revenue;
--
	select name, revenue from (
		select facs.name, sum(case 
					when memid = 0 then slots * facs.guestcost
					else slots * membercost
				end) as revenue
			from cd.bookings bks
			inner join cd.facilities facs
				on bks.facid = facs.facid
			group by facs.name
		) as agg where revenue < 1000
	order by revenue;          

###Postgresql 公用表表达式（CTEs）
>WITH语句通常被称为通用表表达式（Common Table Expressions）或者CTEs。
WITH语句作为一个辅助语句依附于主语句，WITH语句和主语句都可以是SELECT，INSERT，UPDATE，DELETE中的任何一种语句
[讲解](http://www.jasongj.com/sql/cte/)
	
	with name as 一个表
	
	with sum as (select facid,sum(slots) as total from cd.bookings
				group by facid)
	
	select facid,total from sum where total=(select max(total) from sum)
#### 递归
	WITH RECURSIVE NAME(columns) as (
	<initial statement>
	UNION ALL 
	<recursive statement>
	)
--
	with recursive increment(num) as (
		select 1
		union all
		select increment.num + 1 from increment where increment.num < 5
	)
	select * from increment;
--
	with recursive result(recommender) as(
	  select recommendedby from  cd.members where memid=27
	  Union All 
	  select mem.recommendedby
	  from result as recs inner join cd.members  as mem
	  on mem.memid=recs.recommender
	  
	  )
	  
	  select recs.recommender, mems.firstname, mems.surname
		from result recs
		inner join cd.members mems
			on recs.recommender = mems.memid
	order by memid desc
--
多个参数

		with recursive result(member,recmmber) as(
	  select memid,recommendedby from cd.members
	  union all
	  select res.member,mem.recommendedby
	  from result res inner join cd.members as mem
	  on res.recmmber=mem.memid
	  )
	
	select res.member,res.recmmber,mem.firstname,mem.surname
	from result as res inner join cd.members as mem on 	res.recmmber=mem.memid
	where res.member=12 or res.member=22
	order by res.member asc,res.recmmber desc

####ROLLUP(postgresql)
ROLLUP(c1,c2,c3)只生成四个分组集,假设层次结构c1 > c2 > c3如下：

	(c1, c2, c3)
	(c1, c2)
	(c1)
	()

####to_char,trim
to_char(data,text) 把data转换成字符串
[参数详解](http://www.postgres.cn/docs/9.5/functions-formatting.html)

trim（）去空格

	select facs.facid, facs.name,
		trim(to_char(sum(bks.slots)/2.0, '9999999999999999D99')) as "Total Hours"
		
		from cd.bookings bks
		inner join cd.facilities facs
			on facs.facid = bks.facid
		group by facs.facid, facs.name
	order by facs.facid;   

####date_trunc

截断日期函数，例如:

	SELECT DATE_TRUNC('month', TIMESTAMP '2017-03-17 02:09:30');
	
date|
----+
2017-03-01 00:00:00|

###窗口函数

>over（partition by deptno）按照部门分区

	select count(*) over(), firstname, surname
		from cd.members
	order by joindate  

OR

	select count(*) over(partition by date_trunc('month',joindate)),
		firstname, surname
		from cd.members
	order by joindate

OR

	select count(*) over
	(partition by date_trunc('month',joindate) order by joindate asc), 
	count(*) over
	(partition by date_trunc('month',joindate) order by joindate desc), 
		firstname, surname
		from cd.members
	order by joindate

####与over函数结合
>row_number()over()、
>rank()over()和
>dense_rank()over()函数的使用

row_number()只返回一个结果

rank()和dense_rank()可以将所有的都查找出来

* 区别
1. rank()是跳跃排序
2. dense_rank()l是连续排序

以考试为例，如果使用row_number，第一名有两个，只会返回一个
使用rank(),第一名有两个，那么他会直接显示第三名是谁，因为第一名有两个人。
使用dense_rank(),第一名有两个,不影响，它下个会显示第二名。

>ntile(num_buckets integer) ,均匀分区

	select name,case when class=1 then 'high'
				when class=2 then 'average'
				else 'low' end as revence
	from 
	(select fac.name as name,ntile(3) over(order by sum(
	  slots * case when memid=0 then guestcost
	  else membercost end)desc) as class
	  from cd.bookings as book inner join 
	  cd.facilities as fac
	 on book.facid=fac.facid
	  group by fac.name) as ss
	  order by class, name;
	
#### cast，interval

CAST()函数的参数是一个表达式，它包括用AS关键字分隔的源值和目标数据类型。

interval 为取间隔的含义，例如：

dategen.date + interval '1 day' 为dategen.date加上一天。

	select dategen.date,
	(select sum(case
				when memid = 0 then slots * facs.guestcost
				else slots * membercost
			end) as rev
	from cd.bookings bks
			inner join cd.facilities facs
				on bks.facid = facs.facid
	 where bks.starttime > dategen.date - interval '14 days'
				and bks.starttime < dategen.date + interval '1 day'
	) /15 as revenue
	
	from (
	    select  cast(generate_series(timestamp '2012-08-01','2012-08-31','1 day') as date)
	  as date
	  
	  ) as dategen
	 order by dategen.date; 
####时间转换
	select timestamp '2012-08-31 01:00:00'
	select '2012-08-31 01:00:00'::timestamp;
	select cast('2012-08-31 01:00:00' as timestamp);
	
前两种是postgresql的扩展，最后一种是SQL的语法。

--日期转时间戳
	
	SELECT EXTRACT(epoch FROM NOW()); 
	SELECT EXTRACT(epoch FROM CAST('2017-12-06 00:17:10' AS TIMESTAMP)); 
 
--时间戳转日期
	
	SELECT TO_TIMESTAMP(1512490630)
#### like

	select * from cd.facilities where name like 'Tennis%'

不区分大小写：
	
	SQL: select * from cd.facilities where upper(name) like 'TENNIS%'
--
	Postgresql:select * from cd.facilities where name ilike 'Tennis%'
	
####正则匹配
Postgresql:
>Postgres通过〜运算符实现POSIX正则表达式匹配,也可以使用函数POSITION
	
	select memid,telephone from cd.members where telephone ~ '[()]'

SQL:
>* 与LIKE运算符一样，SIMILAR TO使用'_'字符表示'任何字符'，'％'字符表示'任何字符串'。
* TO表达式必须整个字符串，而不只是一个子串在POSIX正则表达式匹配。这意味着通常最终会以“％”字符包围表达式。
* '.' 字符并不代表SIMILAR TO正则表达式中的“任何字符” ：它只是一个简单的字符。

	select memid, telephone from cd.members where telephone similar to '%[()]%';	
	
	
memid|	telephone|
-----+----------
0	  |(000) 000-0000|
3	  |(844) 693-0723|
4	  |(833) 942-4710|

####LPAD
通过字符串填充特定长度的字符串，如果字符串的长度小于设定值，从左边填充，如果字符串的长度大于设定值，将会从右边截断。

LPAD（字符串，长度[，fill_text]）

填充：
	
	SELECT lpad('esource', 13, 'w3r');

LPAD|
---------------+
 w3rw3resource|
截断：

	SELECT lpad('w3resource', 8, 'lpad');

  LPAD|
----------+
 w3resour|

####substr
截字符串

* SUBSTR (str, pos)
* SUBSTR (str, pos, len)

不适用SQL Server

由 <str> 中的第 <pos> 位置开始，选出 <len> 个字节

	SELECT SUBSTR (Store_Name, 3) 
	FROM Geography 
	WHERE Store_Name = 'Los Angeles';
结果:

	's Angeles'

#### regexp_replace(),translate()
替换字符串
 
* TRANSLATE 进行多次单字符替换
	
		TRANSLATE(string, from, to)
>* string 源字符串。
> * from 是第一个参数（string）中应该被替换的一组字符。
> * to 是一组替换from字符串中的字符
> 
>>请注意，如果from长于to，多的字符，在string中将被删除。

		SELECT TRANSLATE('12345', '134', 'ax')

translate|
-----------+
a2x5|
	
* REGEXP_REPLACE 可以在字符串中搜索正则表达式模式。
	
		REGEXP_REPLACE(source, pattern, replacement_string,[, flags])  
>* source
>源是一个应该替换的字符串。
> * pattern
该模式是POSIX正则表达式，用于匹配应替换的字符串。
 >* replacement_string
replacement_string是一个字符串，用于替换与正则表达式模式匹配的子字符串。
> * flags
所述flags参数是一个或控制该功能例如匹配行为多个字符，允许我不区分大小写匹配，正使匹配的任何字符，并且还换行符。		

'g' 指示该函数删除所有字母，而不仅仅是第一个字母 

**'^'**表示字符串的开头，但它还有另外一个含义。当在一组方括号里使用**'^'**是，它表示"非"或"排除"的意思
	select regexp_replace('555-555-5555', '[^0-9]', '', 'g') as telephone

telephone|
---------+
5555555555|

	SELECT REGEXP_REPLACE('ABC12345xyz','[12345]','','g');
re|
---------+
ABCxyz|
	
	SELECT REGEXP_REPLACE('ABC12345xyz','[^12345]','','g');
re|
---------+
12345|
	
## Oracle

####定时任务

一.创建一个存储过程
 	
 	create or replace procedure NAME as
	begin
	  update t_daycut_ctrl t3 set t3.prev_settle_date=TO_DATE(to_char(sysdate,'YYYY-MM-DD'),'YYYY-MM-DD');commit;
	end;
二.创建一个job
	
	declare job_name number;
	begin
	  dbms_job.submit(job_name,
	  'NAME;',
	  sysdate,'多久执行一次'); 
	  end;
三.运行job
>创建好的Job默认是开启的，所以没有必要启动，实在想启动的话，先关闭，再启动

--


先查询Job号：

	SELECT * FROM dba_Jobs a WHERE a.WHAT = 'NAME;';

1：停止Job

	begin
	  dbms_job.broken(249, true);
	  commit;
	end;

其中 249 为查询出来的Job号

2：启动Job

	begin
	  dbms_job.run(249);
	  commit;
	end;


如果想删除Job

	begin
	  dbms_job.remove(247);
	  commit; 
	end;
	
>或者省略第一步：
		
	declare job_name number;
	begin
	  dbms_job.submit(job_name,
	  'update t_daycut_ctrl t3 set t3.prev_settle_date=TO_DATE(to_char(sysdate,'YYYY-MM-DD'),'YYYY-MM-DD');',
	  sysdate,'多久执行一次'); 
	  end;

四、补充
	
	SELECT * FROM dba_Jobs a WHERE a.WHAT = 'PROC_DAYCUT_CTRL_UPDATE;';

查询出来字段解释：

dba_jobs中几个比较重要的字段

job: 指的是job的id号。比如上面的 41

failures：job执行的时候失败次数，如果超过了15次，那么broken列将被标为Y，以后就不会运行该job了

broken：默认为N，如果为Y，意味着不再执行该job！

interval：执行job的间隔时间。

what：该job的实际工作。

其中：

描述 INTERVAL参数值

1:每分钟执行

	Interval => TRUNC(sysdate,'mi') + 1/ (24*60)

或

	Interval => sysdate+1/1440

2:每天定时执行

例如：每天的凌晨1点执行

	Interval => TRUNC(sysdate) + 1 +1/ (24)

3:每周定时执行

例如：每周一凌晨1点执行

	Interval => TRUNC(next_day(sysdate,'星期一'))+1/24

4:每月定时执行

例如：每月1日凌晨1点执行
	
	Interval =>TRUNC(LAST_DAY(SYSDATE))+1+1/24

5:每季度定时执行

例如每季度的第一天凌晨1点执行

	Interval => TRUNC(ADD_MONTHS(SYSDATE,3),'Q') + 1/24

6:每半年定时执行

例如：每年7月1日和1月1日凌晨1点

	Interval => ADD_MONTHS(trunc(sysdate,'yyyy'),6)+1/24

7:每年定时执行

例如：每年1月1日凌晨1点执行

	Interval =>ADD_MONTHS(trunc(sysdate,'yyyy'),12)+1/24


####Oracle trunc

>trunc:TRUNC函数为指定元素而截去的日期值。

	1.select trunc(sysdate) from dual  --2011-3-18  今天的日期为2011-3-18
	2.select trunc(sysdate, 'mm')   from   dual  --2011-3-1    返回当月第一天.
	3.select trunc(sysdate,'yy') from dual  --2011-1-1       返回当年第一天
	4.select trunc(sysdate,'dd') from dual  --2011-3-18    返回当前年月日
	5.select trunc(sysdate,'yyyy') from dual  --2011-1-1   返回当年第一天
	6.select trunc(sysdate,'d') from dual  --2011-3-13 (星期天)返回当前星期的第一天
	7.select trunc(sysdate, 'hh') from dual   --2011-3-18 14:00:00   当前时间为14:41   
	8.select trunc(sysdate, 'mi') from dual  --2011-3-18 14:41:00   TRUNC()函数没有秒的精确

####Oracle REGEXP_INSTR

>函数让你搜索一个正则表达式模式字符串。函数使用输入字符集定义的字符进行字符串的计算。
它返回一个整数，指示开始或结束匹配的子位置.
[参考](https://blog.csdn.net/itmyhome1990/article/details/50379729)

该|模式用于像一个”或”指定多个替代方案

例子：

	SELECT REGEXP_INSTR ('Itmyhome', 'a|i|o|e|u')
	FROM dual;

####Oracle decode
>decode(条件,值1,返回值1,值2,返回值2,...值n,返回值n,缺省值)
decode(条件,值1,返回值1,值2,返回值2,...值n,返回值n,缺省值)

	该函数的含义如下：
	IF 条件=值1 THEN
	　　　　RETURN(翻译值1)
	ELSIF 条件=值2 THEN
	　　　　RETURN(翻译值2)
	　　　　......
	ELSIF 条件=值n THEN
	　　　　RETURN(翻译值n)
	ELSE
	　　　　RETURN(缺省值)
	END IF
>decode(字段或字段的运算，值1，值2，值3）
 这个函数运行的结果是，当字段或字段的运算的值等于值1时，该函数返回值2，否则返回值3
 当然值1，值2，值3也可以是表达式，这个函数使得某些sql语句简单了许多


####Oracle substr

>substr(字符串,截取开始位置,截取长度) //返回截取的字

	substr('Hello World',0,1) //返回结果为 'H'  *从字符串第一个字符开始截取长度为1的字符串
	
	substr('Hello World',1,1) //返回结果为 'H'  *0和1都是表示截取的开始位置为第一个字符
	
	substr('Hello World',2,4) //返回结果为 'ello'
	
	substr('Hello World',-3,3)//返回结果为 'rld' *负数(-i)表示截取的开始位置为字符串右端向左数第i个字符

####Oracle nvl
	NVL(eExpression1, eExpression2)
	eExpression1, eExpression2

>如果 eExpression1 的计算结果为 null 值，则 NVL( ) 返回 eExpression2。如果 eExpression1 的计算结果不是 null 值，则返回 eExpression1。eExpression1 和 eExpression2 可以是任意一种数据类型。如果 eExpression1 与 eExpression2 的结果皆为 null 值，则 NVL( ) 返回NULL.。

####Oracle instr
	instr(String,str[,start_position[,times_appearence]])
>String:字符;

>str：目标字符;

>start_position:查找的起始位置，默认是1，表示从第一个字符开始查找。

>times_appearence：查找第几次出现的str，默认是1，表示查找的是第一次出现的str。

>中括号表示：括号中的内容可有可无；

>中括号中的中括号表示：必须先有外面中括号的参数，然后才会可以设置内中括号的参数。
	
	select instr('abc','a') from dual;    -- 返回 1 
	select instr('abc','bc') from dual; -- 返回 2
	select instr('abc abc','a',1,2) from dual; -- 返回 5
	select instr('abc','bc',-1,1) from dual; -- 返回 2
	select instr('abc','d') from dual; -- 返回 0
	
####Oracle  ceil 、floor
>ceil(n) 取大于等于数值n的最小整数；

>floor(n)取小于等于数值n的最大整数

	SQL> select ceil(9.5) from dual;
	 
	 CEIL(9.5)
	----------
	        10
	 
	SQL> select floor(9.5) from dual;
	 
	FLOOR(9.5)
	----------
	         9

####Oracle  删除字符
##### TRANSLATE
>	语法：TRANSLATE(char, from, to)
>用法：返回将出现在from中的每个字符替换为to中的相应字符以后的字符串。
            若from比to字符串长，那么在from中比to中多出的字符将会被删除。
            三个参数中有一个是空，返回值也将是空值。
            
	　　SELECT TRANSLATE('abcdefghij','abcdef','123456') FROM dual;　　--返回 123456ghij
	　　SELECT TRANSLATE('abcdefghij','abcdefghij','123456') FROM dual;　　--返回 123456
	　　SELECT TRANSLATE('00abcf00','abc','123') newstr FROM dual;　　 --返回 00123f00
	　　SELECT TRANSLATE('abcbbaadef','ba','#@') FROM dual　--（b将被＃替代，a将被＠替代）返回 @#c##@@def
	　　SELECT TRANSLATE('abcbbaadef','bad','#@') FROM dual　--（b将被＃替代，a将被＠替代，d对应的值是空值，将被移走）

#####Trim

>Trim 函数具有删除任意指定字符的功能，而去除字符串首尾空格则是trim函数被使用频率最高的一种。语法Trim ( string ) ，参数string：string类型，指定要删除首部和尾部空格的字符串返回值String

>
	如果指定LEADING，则Oracle Database将删除任何等于的前导字符trim_character。
	
	如果指定TRAILING，则Oracle删除任何等于的尾随字符trim_character。
	
	如果您指定BOTH或不指定三者，则Oracle会删除等于的前导和尾随字符trim_character。
	
	如果未指定trim_character，则默认值为空格。
	
	如果仅指定trim_source，则Oracle将删除前导和尾随空格。
	
	该函数返回一个具有datatype的值VARCHAR2。值的最大长度是长度trim_source。
	
	如果其中一个trim_source或trim_character为null，则该TRIM函数返回null。
	
	双方trim_character并trim_source可以是任何数据类型CHAR，VARCHAR2，NCHAR，NVARCHAR2，CLOB，或NCLOB。返回的字符串是VARCHAR2数据类型，如果trim_source是字符数据类型，则trim_source是LOB，如果是LOB数据类型。返回字符串与字符集相同trim_source。



例子:
 	
 	select trim(leading from '   11  ') aa from dual;
 	
 	结果：
 	11

 	select trim(leading 'i' from ' i11  ') aa from dual;
 	结果(会有一个空格，首位没有找到’i‘，所以都没有替换):
 	 i11
 	 
 	select trim(leading 'i' from 'i11  ') aa from dual;
 	
 	结果:
 	i11
#####Replace
>replace 函数用第三个表达式替换第一个字符串表达式中出现的所有第二个给定字符串表达式
	
[参考](https://blog.csdn.net/netswing/article/details/6743556)

例子：
	
	 select replace('   aa  kk  ',' ','') abcd from dual;
	 
####Oracle 循环
1.ORACLE中的GOTO用法
 
	DECLARE
	  x number;
	BEGIN
	  x := 9;
	  <<repeat_loop>> --循环点
	  x := x - 1;
	  DBMS_OUTPUT.PUT_LINE(X);
	  IF X > 0 THEN
	    GOTO repeat_loop; --当x的值小于9时,就goto到repeat_loop
	  END IF;
	END;
　　 
2.ORACLE中的FOR循环用法
	
	DECLARE
	  X number; --声明变量
	BEGIN
	  x := 1; --给初值
	  FOR X IN REVERSE 1 .. 10 LOOP
	    --reverse由大到小
	    DBMS_OUTPUT.PUT_LINE('内:x=' || x);
	  END LOOP;
	  DBMS_OUTPUT.PUT_LINE('end loop:x=' || X); --x=1
	END;
 
3.ORACLE中的WHILE循环用法
 
	DECLARE
	  x number;
	BEGIN
	  x := 0;
	  WHILE x < 9 LOOP
	    x := x + 1;
	    DBMS_OUTPUT.PUT_LINE('内:x=' || x);
	  END LOOP;
	  DBMS_OUTPUT.PUT_LINE('外:x=' || x);
	END;
 

 
4.ORACLE中的LOOP循环用法

	DECLARE
	  x number;
	BEGIN
	  x := 0;
	  LOOP
	    x := x + 1;
	  
	    EXIT WHEN x > 9;
	    DBMS_OUTPUT.PUT_LINE('内:x=' || x);
	  END LOOP;
	  DBMS_OUTPUT.PUT_LINE('外:x=' || x);
	END;

####Oracle 游标
[参考内容](https://www.cnblogs.com/xiaoliu66007/p/7495753.html)

	隐式游标的属性 返回值类型   意    义   
	SQL%ROWCOUNT    整型  代表DML语句成功执行的数据行数   
	SQL%FOUND   布尔型 值为TRUE代表插入、删除、更新或单行查询操作成功   
	SQL%NOTFOUND    布尔型 与SQL%FOUND属性返回值相反   
	SQL%ISOPEN  布尔型 DML执行过程中为真，结束后为假  

####Oracle （+）

>外连接， 也可以使用“(+) ”来表示。 关于使用（+）的一些注意事项：

	1.（+）操作符只能出现在WHERE子句中，并且不能与OUTER JOIN语法同时使用。
	2.当使用（+）操作符执行外连接时，如果在WHERE子句中包含有多个条件，则必须在所有条件中都包含（+）操作符。
	3.（+）操作符只适用于列，而不能用在表达式上。
	4.（+）操作符不能与OR和IN操作符一起使用。
	5.（+）操作符只能用于实现左外连接和右外连接，而不能用于实现完全外连接。

>示例	
>>用（+）来实现， 这个+号可以这样来理解： 

>> +表示补充，即哪个表有加号，这个表就是匹配表。

>>如果加号写在右表，左表就是全部显示，所以是左连接。
	
	Select * from t_A a,t_B b where a.id=b.id(+);
>它的结果集是t_A表中的全部数据，再加上t_A表和t_B表匹配后的数据。换句话说,左表(t_A)的记录将会全部表示出来,而右表(t_B)只会显示符合搜索条件的记录。t_B表记录不足的地方均为NULL。
	
	Select * from t_A a,t_B b where a.id(+)=b.id;
>以右表(t_B)为基础的。它的结果集是t_B表所有记录，再加上t_A和t_B匹配后的数据。 t_A表记录不足的地方均为NULL。

####Oracle if

	一、单个IF
	1、
	
	if a=...  then
	.........
	end if;
	
	2、
	
	if a=... then
	......
	else
	....
	end if;
	
	二、多个IF
	
	if a=..  then
	......
	elsif a=..  then
	....
	end if;     
	这里中间是“ELSIF”，而不是ELSE IF 。这里需要特别注意

####Oracle EXECUTE IMMEDIATE
[参考内容1](https://www.cnblogs.com/zj0208/p/6088097.html)

[参考内容2](https://www.cnblogs.com/xwb583312435/p/9056263.html)
	
	create or replace procedure proc_test(
	--参数区域
	)
	is 
	--变量区域
	    --sql脚本
	    v_sql varchar2(2000) :='';
	    --记录学生数量
	    v_num number;
	begin
	--执行区域
	
	    -- execute immediate用法1：立刻执行sql语句
	    v_sql := 'create or replace view myview as select id,name from student';
	    execute immediate v_sql;
	    
	    --- execute immediate用法2：立刻执行sql语句，并赋值给某个变量
	    v_sql := 'select count(1) from student';
	    execute immediate v_sql into v_num;
	    
	    -- execute immediate用法3：带参数的sql
	    v_sql:='select * from student t where t.name=:1 and t.age=:2'; 
	    execute immediate v_sql using 'ZhangSan',23;
	    
	end proc_test;

####Oracle REGEXP_SUBSTR
>这个函数的作用是正则分隔字符串，用法为

	function REGEXP_SUBSTR(String, pattern, position, occurrence, modifier)

 
>String：操作的字符串；

>pattern：正则表达式匹配规则，匹配到则返回；

>position：开始匹配的位置，默认当然是1；

>occurrence：标识第几个匹配组，默认为1

>modifier：模式（‘i‘不区分大小写进行检索，‘c‘区分大小写进行检索。默认为‘c‘）

1、查询使用正则分割后的第一个值，也就是17

	SELECT REGEXP_SUBSTR('17,20,23','[^,]+',1,1,'i') AS STR FROM DUAL;
	
	结果：17

2、查询使用正则分割后的最后一个值，也就是23


	SELECT REGEXP_SUBSTR('17,20,23','[^,]+',1,3,'i') AS STR FROM DUAL;  
	
	结果:  23
	


### Oracle 判断表是否存在

	declare num   number;
	   begin
	   --判断所有的表中是否已经存在这个表
	          select count(1) into num from all_tables where TABLE_NAME = upper('sjtp_jsz_num');
	          --and OWNER=upper('username');
	          if   num=1   then
	          --如果存在，则执行drop table
	               execute immediate 'drop table sjtp_jsz_num';
	          end   if;
	          --新建table，实现全表更新
	          execute immediate 'create table sjtp_jsz_num as select * from sjtp_wf_num';
	   end;
	   
### Oracle 日期
[参考](https://www.cnblogs.com/linximf/archive/2011/11/21/2257036.htmlf)

### Oralce Merge

[参考文档](http://www.cnblogs.com/highriver/archive/2011/08/02/2125043.html)

**Merge使用场景:**
 	
 	使用一条语句从一个或者多个数据源中完成对表的更新和插入数据
 
>我们有一个任务，有一个表T，有两个字段a,b，我们想在表T中做Insert/Update,如果存在，则更新T中b的值，如果不存在，则插入一条记录。在Microsoft的SQL语法中，很简单的一句判断就可以了，SQL Server中的语法如下：

	if exists(select 1 from T where T.a='1001' ) 
	update T set T.b=2 Where T.a='1001' else 
	insert into T(a,b) values('1001',2);

以上语句表明当T表中如果存在a='1001' 的记录的话，就把b的值设为2，否则就Insert一条a='100'，b=2的记录到T中。

	MERGE INTO T T1
	USING (SELECT a,b FROM T WHERE t.a='1001') T2
	ON ( T1.a=T2.a)
	WHEN MATCHED THEN
	  UPDATE SET T1.b = 2
	WHEN NOT MATCHED THEN 
	  INSERT (a,b) VALUES('1001',2);

