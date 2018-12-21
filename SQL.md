#SQL

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
	