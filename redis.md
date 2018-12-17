#Redis
[初识教程](http://www.runoob.com/redis/redis-tutorial.html)
###MAC
1.[安装](https://blog.csdn.net/jason_m_ho/article/details/80007330)
2.服务器启动:
  redis-server
3.启动成功:
  ps axu|grep redis
4.客户端启动:
  redis-cli
  
##redis
### redis 结构
Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。
![](../img/redis.png)

注意：Redis支持多个数据库，并且每个数据库的数据是隔离的不能共享，并且基于单机才有，如果是集群就没有数据库的概念


Redis是一个**字典结构**的存储服务器，而实际上一个Redis实例提供了多个用来存储数据的字典，客户端可以指定将数据存储在哪个字典中。这与我们熟知的在一个关系数据库实例中可以创建多个数据库类似，所以可以将其中的每个字典都理解成一个独立的数据库。

每个数据库对外都是一个从0开始的递增数字命名，Redis默认支持16个数据库（可以通过配置文件支持更多，无上限），可以通过配置databases来修改这一数字.
###命令
一、

1. select num#选择库,默认在0库,共16个库
2. quit      #退出连接
3. info      #获得服务的信息与统计
4. monitor   #实时监控
5. config get#获得服务配置
6. flushdb   #删除当前选择的数据库中的key
7. flushall  #删除所有数据库中的key
8. del key   #该命令用于在 key 存在时删除 key。
9. AUTH password #验证密码是否正确
10. PING    #查看服务是否运行

二、

远程服务上执行命令

	redis-cli -h host -p port -a password

有时候会有中文乱码。
	
	redis-cli --raw
就可以避免中文乱码了

### redis 事务
>Redis 事务可以一次执行多个命令， 并且带有以下两个重要的保证：

>批量操作在发送 EXEC 命令前被放入队列缓存。
收到 EXEC 命令后进入事务执行，**事务中任意命令执行失败，其余的命令依然被执行。
**在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。
一个事务从开始到执行会经历以下三个阶段：

>>开始事务。

>>命令入队。

>>执行事务。
  
  单个 Redis 命令的执行是原子性的，但 Redis 没有在事务上增加任何维持原子性的机制，所以 Redis 事务的执行并不是原子性的。

事务可以理解为一个打包的批量执行脚本，但批量指令并非原子化的操作，中间某条指令的失败不会导致前面已做指令的回滚，也不会造成后续的指令不做。

