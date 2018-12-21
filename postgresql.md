
#Postgresql
tablename 表名
weather 表名
cities 表名
column_name 列名

1.**创建表**

	CREATE TABLE tablename (
	    city            varchar(80),
	    temp_lo         int,           -- 最低温度
	    temp_hi         int,           -- 最高温度
	    prcp            real,          -- 湿度
	    date            date
	);
point 类型坐标

2.***删除表***

	DROP TABLE tablename

3.***对数据的增删改查***

（1）**插入数据**

表有5列
1.insert into .. values(..)
--
	INSERT INTO tablename VALUES ('San Francisco',	46,50,0.25,'1994-11-27');

请注意所有数据类型都使用了相当明了的输入格式。那些不是简单数字值的常量通常必需用单引号（'）包围
可以使用另一种写法，根据列名进行插入

	INSERT INTO tablename (column_name, city, column_name, column_name)
    VALUES ('1994-11-29', 'Hayward', 54, 37),
    ('1994-11-29', 'Hayward', 54, 37);
2. insert into .. select ..
--	
 	insert into cd.facilities
    SELECT 9, 'Spa', 20, 30, 100000, 800
    UNION ALL
    SELECT 10, 'Squash Court 2', 3.5, 17.5, 5000, 80;
 
你还可以使用COPY从文本文件中装载大量数据。比如:
COPY weather FROM '/home/user/weather.txt';
 
 (2)**查找数据**
 	
 	SELECT * FROM tablename
 数据相加:
 
 	SELECT city, (temp_hi+temp_lo)/2 AS temp_avg, date FROM weather;
 
 WHERE子句包含一个布尔（真值）表达式，只有那些使布尔表达式为真的行才会被返回。在条件中可以使用常用的布尔操作符（AND、OR和NOT）:
 
 	SELECT * FROM weather
    WHERE city = 'San Francisco' AND prcp > 0.0;
 
 要求返回的查询结果是排好序:
 	
 	SELECT * FROM weather
    ORDER BY city;
    
 可以添加多个排序值:
 	
 	SELECT * FROM weather
    ORDER BY city, temp_lo;
      
  查询的结果中消除重复的行:
  	
  	SELECT DISTINCT city
    FROM weather;
  组合使用DISTINCT和ORDER BY来保证获取一致的结果:
  	
  	SELECT DISTINCT city
    FROM weather
    ORDER BY city;
**表之间连接**
*内连接*
只返回两个表中联结**字段相等的行**
 -1- 
 	
 	SELECT *
	FROM weather, cities
	WHERE city = name;
 -2-
	
	SELECT *
    FROM weather INNER JOIN 
    cities ON (weather.city = cities.name);
 两种不同的表现形式
 
 *左外连接*
 	
 	SELECT *
    FROM weather LEFT OUTER JOIN 
    cities ON (weather.city = cities.name);
 OUTER 可以省去
 *右外连接*
  	
    SELECT *
    FROM weather RIGHT OUTER JOIN 
    cities ON (weather.city = cities.name);
  OUTER 可以省去
  
   *全外连接*
 返回参与连接的两个数据集合中的**全部数据**
 ![](../img/full_outer.png)
 返回蓝色部分
     
     SELECT *
     FROM weather FULL OUTER JOIN cities ON                 (weather.city = cities.name);
 *自连接*
 	
 	SELECT W1.city, W1.temp_lo AS low, W1.temp_hi AS high,
    W2.city, W2.temp_lo AS low, W2.temp_hi AS high
    FROM weather W1, weather W2
    WHERE W1.temp_lo < W2.temp_lo
    AND W1.temp_hi > W2.temp_hi;

(3)**更新数据**

假设你发现所有 11 月 28 日以后的温度读数都低了两度，那么你就可以用下面的方式改正数据：
1.update .. set..
--		
	UPDATE weather
    SET temp_hi = temp_hi - 2,  
    temp_lo = temp_lo - 2
    WHERE date > '1994-11-28';

2.update .. set..(可以加子查询)
--
	update cd.facilities facs
	    set
	        membercost = (select membercost * 1.1 from cd.facilities where facid = 0),
	        guestcost = (select guestcost * 1.1 from cd.facilities where facid = 0)
	    where facs.facid = 1;

3. update .. set .. from         	    
--
	update cd.facilities facs
	    set
	        membercost = facs2.membercost * 1.1,
	        guestcost = facs2.guestcost * 1.1
	    from (select * from cd.facilities where facid = 0) facs2
	    where facs.facid = 1;
    
（4）**删除数据**
1.delete from ..
--
	DELETE FROM weather WHERE city = 'Hayward';
2. truncate
--
	 truncate  cd.bookings;
	  truncate table cd.bookings;
	  一样
**慎用**，速度比较快，但是如果涉及事务，还是需要delete
[delete和truncation的区别](https://www.cnblogs.com/8765h/archive/2011/11/25/2374167.html)

4.***聚集函数***

一个聚集函数从多个输入行中计算出一个结果。 比如，我们有在一个行集合上计算count（计数）、sum（和）、avg（均值）、max（最大值）和min（最小值）的函数
  
  比如，我们可以用下面的语句找出所有记录中最低温度中的最高温度
	
	SELECT max(temp_lo) FROM weather;
 **错误:**
 	
	SELECT city FROM weather 
	WHERE temp_lo = max(temp_lo);  
因为聚集max不能被用于WHERE子句中（存在这个限制是因为WHERE子句决定哪些行可以被聚集计算包括；因此显然它必需在聚集函数之前被计算，可以使用子查询。

	SELECT city FROM weather
    WHERE temp_lo = (SELECT max(temp_lo) 
    FROM weather);
    
分组，获取每个城市观测到的最低温度的最高值:
	
	SELECT city, max(temp_lo)
    FROM weather
    GROUP BY city;
每个聚集结果都是在匹配该城市的表行上面计算的。我们可以用HAVING 过滤这些被分组的行
	
	SELECT city, max(temp_lo)
    FROM weather
    GROUP BY city
    HAVING max(temp_lo) < 40;
    
如果我们只关心那些名字以“S”开头的城市，我们可以用:
	
	SELECT city, max(temp_lo)
    FROM weather
    WHERE city LIKE 'S%'            -- (1)
    GROUP BY city
    HAVING max(temp_lo) < 40;  
  
 (1)LIKE操作符进行模式匹配
 
 *WHERE和HAVING的基本区别如下*
 >WHERE在分组和聚集计算之前选取输入行（因此，它控制哪些行进入聚集计算）， 而HAVING在分组和聚集之后选取分组行。因此，WHERE子句不能包含聚集函数； 因为试图用聚集函数判断哪些行应输入给聚集运算是没有意义的。相反，HAVING子句总是包含聚集函数（严格说来，你可以写不使用聚集的HAVING子句， 但这样做很少有用。同样的条件用在WHERE阶段会更有效）。
 
5.***高级特性***

（1）**视图**

假设天气记录和城市为止的组合列表对我们的应用有用，但我们又不想每次需要使用它时都敲入整个查询。我们可以在该查询上创建一个视图，这会给该查询一个名字，我们可以像使用一个普通表一样来使用它：
	
	CREATE VIEW myview AS
    SELECT city, temp_lo, 
    temp_hi, prcp, date, location
        FROM weather, cities
        WHERE city = name;
-
	SELECT * FROM myview;
	
 (2)**外键**
 
 我们希望确保在cities表中有相应项之前任何人都不能在weather表中插入行。这叫做维持数据的引用完整性
 
	 CREATE TABLE cities (
	        city     varchar(80) primary key,
	        location point
	);
	
	CREATE TABLE weather (
	        city      varchar(80) references cities(city),
	        temp_lo   int,
	        temp_hi   int,
	        prcp      real,
	        date      date
	);
	
尝试插入一个非法的记录:
	
	INSERT INTO weather VALUES ('Berkeley', 45, 53, 0.0, '1994-11-28');

>ERROR:  insert or update on table "weather" violates foreign key constraint "weather_city_fkey"
DETAIL:  Key (city)=(Berkeley) is not present in table "cities".

因为city已经关联，所以cities表中city必须和weather中的city保持一致，但是可以不插入city，插入其他数据（city可以为空时).

(3)**事务**
>**事务是所有数据库系统的基础概念。事务最重要的一点是它将多个步骤捆绑成了一个单一的、要么全完成要么全不完成的操作。步骤之间的中间状态对于其他并发事务是不可见的，并且如果有某些错误发生导致事务不能完成，则其中任何一个步骤都不会对数据库造成影响**
>>例如，考虑一个保存着多个客户账户余额和支行总存款额的银行数据库。假设我们希望记录一笔从Alice的账户到Bob的账户的额度为100.00美元的转账。在最大程度地简化后，涉及到的SQL命令是：

	UPDATE accounts SET balance = balance - 100.00
	    WHERE name = 'Alice';
	UPDATE branches SET balance = balance - 100.00
	    WHERE name = (SELECT branch_name FROM accounts WHERE name = 'Alice');
	UPDATE accounts SET balance = balance + 100.00
	    WHERE name = 'Bob';
	UPDATE branches SET balance = balance + 100.00
	    WHERE name = (SELECT branch_name FROM accounts WHERE name = 'Bob');

**事物特性**

(1)原子性:当操作中途某些错误发生时已经执行的步骤不会产生效果。将这些更新组织成一个事务就可以给我们这种保障。一个事务被称为是**原子的**：从其他事务的角度来看，它要么整个发生要么完全不发生.

(2)持久性:我们同样希望能保证一旦一个事务被数据库系统完成并认可，它就被永久地记录下来且即便其后发生崩溃也不会被丢失。
一个事务型数据库保证一个事务在被报告为完成之前它所做的所有更新都被记录在**持久存储**（即磁盘）

（3)隔离性:当多个事务并发运行时，每一个都不能看到其他事务未完成的修改.
 
 (4)一致性:　一致性是指事务必须使数据库从一个一致性状态变换到另一个一致性状态，也就是说一个事务执行之前和执行之后都必须处于一致性状态。
   
   在PostgreSQL中，开启一个事务需要将SQL命令用**BEGIN和COMMIT**命令包围起来。因此我们的银行事务看起来会是这样：
	  
	BEGIN;
	UPDATE accounts SET 
		balance = balance - 100.00
	WHERE name = 'Alice';
	-- etc etc
	COMMIT;
	
发出ROLLBACK命令而不是COMMIT命令，这样所有目前的更新将会被取消

>利用**保存点**来以更细的粒度来控制一个事务中的语句。保存点允许我们有选择性地放弃事务的一部分而提交剩下的部分。在使用**SAVEPOINT**定义一个保存点后，我们可以在必要时利用**ROLLBACK TO**回滚到该保存点。该事务中位于保存点和回滚点之间的数据库修改都会被放弃，但是早于该保存点的修改则会被保存

例子:
	
	UPDATE accounts SET balance = balance - 100.00
	    WHERE name = 'Alice';
	SAVEPOINT my_savepoint;
	UPDATE accounts SET balance = balance + 100.00
	    WHERE name = 'Bob';
	-- oops ... forget that and use Wally's account
	ROLLBACK TO my_savepoint;
	UPDATE accounts SET balance = balance + 100.00
	    WHERE name = 'Wally';
	COMMIT;
	
ROLLBACK TO是唯一的途径来重新控制一个由于错误被系统置为中断状态的事务块，而不是完全回滚它并重新启动。

(4)**窗口函数**

例子用于展示如何将每一个员工的薪水与他/她所在部门的平均薪水进行比较
	SELECT depname, empno, salary, avg(salary) OVER (PARTITION BY depname) FROM empsalary;
	
最开始的三个输出列直接来自于表empsalary，并且表中每一行都有一个输出行。第四列表示对与当前行具有相同depname值的所有表行取得平均值（这实际和非窗口avg聚集函数是相同的函数，但是OVER子句使得它被当做一个窗口函数处理并在一个合适的窗口帧上计算。）
>一个窗口函数调用总是包含一个直接跟在窗口函数名及其参数之后的**OVER子句**。这使得它从句法上和一个普通函数或非窗口函数区分开来。OVER子句决定究竟查询中的哪些行被分离出来由窗口函数处理。OVER子句中的PARTITION BY子句指定了将**具有相同PARTITION BY表达式值**的行分到组或者分区。对于每一行，窗口函数都会在当前行同一分区的行上进行计算。

	SELECT salary, sum(salary) OVER () FROM empsalary;
   salary |  sum
   --------+-------
   5200 | 47100
   5000 | 47100
   3500 | 47100
   4800 | 47100
>由于在OVER子句中没有ORDER BY，窗口帧和分区一样，而如果缺少PARTITION BY则和整个表一样。换句话说，每个合计都会在整个表上进行，这样我们为每一个输出行得到的都是相同的结果。但是如果我们加上一个ORDER BY子句，我们会得到非常不同的结果
	
	SELECT salary, sum(salary) OVER (ORDER BY salary) FROM empsalary;

 salary |  sum
--------+-------
   3500 |  3500
   3900 |  7400
   4200 | 11600
   4500 | 16100

>这里的合计是从第一个（最低的）薪水一直到当前行，包括任何与当前行相同的行（注意相同薪水行的结果

>>窗口函数只允许出现在查询的SELECT列表和ORDER BY子句中。它们不允许出现在其他地方，例如GROUP BY、HAVING和WHERE子句中。这是因为窗口函数的执行逻辑是在处理完这些子句之后。另外，窗口函数在非窗口聚集函数之后执行。这意味着可以在窗口函数的参数中包括一个聚集函数，但反过来不行。
----
>>如果需要在窗口计算执行后进行过滤或者分组，我们可以使用子查询

当一个查询涉及到多个窗口函数时，可以将每一个分别写在一个独立的OVER子句中。但如果多个函数要求同一个窗口行为时，这种做法是冗余的而且容易出错的。替代方案是，每一个窗口行为可以被放在一个命名的WINDOW子句中，然后在OVER中引用它。例如：

	SELECT sum(salary) OVER w, avg(salary) OVER w
	  FROM empsalary
	  WINDOW w AS (PARTITION BY depname ORDER BY salary DESC);
	  
(5)继承

	CREATE TABLE cities (
	  name       text,
	  population real,
	  altitude   int     -- (in ft)
	);
	
	CREATE TABLE capitals (
	  state      char(2)
	) INHERITS (cities);


>一个capitals的行从它的父亲cities继承了所有列（name、population和altitude）。列name的类型是text，一种用于变长字符串的本地PostgreSQL类型。州首都有一个附加列state用于显示它们的州。在PostgreSQL中，一个表可以从0个或者多个表继承

	
	SELECT name, altitude
	  FROM cities
	  WHERE altitude > 500;
	  
name    | altitude
-----------+----------
 Las Vegas |     2174
 Mariposa  |     1953
 Madison   |      845

	SELECT name, altitude
	    FROM ONLY cities
	    WHERE altitude > 500;
name    | altitude
-----------+----------
 Las Vegas |     2174
 Mariposa  |     1953
 
>其中cities之前的ONLY用于指示查询只在cities表上进行而不会涉及到继承层次中位于cities之下的其他表。很多我们已经讨论过的命令 — SELECT、UPDATE 和DELETE — 都支持这个ONLY记号。

**注意**
尽管继承很有用，但是它还未与唯一约束或外键集成，这也限制了它的可用性。