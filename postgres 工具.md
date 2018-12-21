#postgresql 工具

## postgres_fdw

它可以被用来访问存储在外部PostgreSQL服务器中的数据。

这个模块提供的功能大体上覆盖了较老的dblink模块的功能。但是postgres_fdw提供了更透明且更兼容标准的语法来访问远程表，并且可以在很多情况下给出更好的性能。

一般使用postgres_fdw做远程访问，需要四步：

1. 添加扩展 使用**CREATE EXTENSION** 来安装postgres_fdw扩展
2. 添加服务器 使用**CREATE SERVER** 创建一个外部服务器对象，它用来表示它用来表示你想连接的每一个远程数据库。指定除了user和password之外的连接信息作为该服务器对象的选项。
3. 添加用户 使用**CREATE USER MAPPING** 创建一个用户映射，每一个用户映射都代表你想允许一个数据库用户访问一个外部服务器。指定远程用户名和口令作为用户映射的user和password选项
4. 添加表 使用**CREATE FOREING TABLE** 或者 **IMPORT FOREING SCHEMA** 外部表的列必须匹配被引用的远程表，但是，如果你在外部表对象的选项中指定了正确的远程名称，你可以使用不同于远程表的表名或列名。

每步需要使用命令的含义：
 
 1. *CREATE EXTENSION (扩展)*
	
		 CREATE EXTENSION [ IF NOT EXISTS ] extension_name
	    [ WITH ] [ SCHEMA schema_name ]
	             [ VERSION version ]
	             [ FROM old_version ]
	             [ CASCADE ]
	 [参数详解](http://www.postgres.cn/docs/10/sql-createextension.html)
 2. *CREATE SERVER （服务器）*
 		
	 		CREATE SERVER 
	 		[IF NOT EXISTS] 
	 		server_name [ TYPE 'server_type' ] 
	 		[ VERSION 'server_version' ]
	    FOREIGN DATA WRAPPER fdw_name
	    [ OPTIONS ( option 'value' [, ... ] ) ]
	
	[参数详解](http://www.postgres.cn/docs/10/sql-createserver.html)

 3. *CREATE USER MAPPING （用户）*
	 	
	 	CREATE USER MAPPING 
	 	[IF NOT EXISTS] FOR 
	 	{ user_name | USER | CURRENT_USER | PUBLIC }
	    SERVER server_name
	    [ OPTIONS ( option 'value' [ , ... ] ) ]	
	    	
	[参数详解](http://www.postgres.cn/docs/10/sql-createusermapping.html)
	
 4. *CREATE FOREIGN TABLE （创建表）*
		   
		   CREATE FOREIGN TABLE 
		   [ IF NOT EXISTS ] table_name ( [
		  { column_name data_type 
		  [ OPTIONS ( option 'value' [, ... ] ) ] 
		  [ COLLATE collation ][ column_constraint [ ... ] ]
		    | table_constraint }
		    [, ... ]
		] )
		[ INHERITS ( parent_table [, ... ] ) ]
		  SERVER server_name
		[ OPTIONS ( option 'value' [, ... ] ) ]
___
		
		CREATE FOREIGN TABLE [ IF NOT EXISTS ] table_name
		  PARTITION OF parent_table [ (
		  { column_name [ WITH OPTIONS ] [ column_constraint [ ... ] ]
		    | table_constraint }
		    [, ... ]
		) ] partition_bound_spec
		  SERVER server_name
		[ OPTIONS ( option 'value' [, ... ] ) ]
___		

		其中 column_constraint 是：
		
		[ CONSTRAINT constraint_name ]
		{ NOT NULL |
		  NULL |
		  CHECK ( expression ) [ NO INHERIT ] |
		  DEFAULT default_expr }
		
		而 table_constraint 是：
		
		[ CONSTRAINT constraint_name ]
		CHECK ( expression ) [ NO INHERIT ]
		
 [参数详解](http://www.postgres.cn/docs/10/sql-createforeigntable.html)
 
 *IMPORT FOREIGN SCHEMA (导入表定义)*
 
	    IMPORT FOREIGN SCHEMA remote_schema
	    [ { LIMIT TO | EXCEPT } ( table_name [, ...] ) ]
	    FROM SERVER server_name
	    INTO local_schema
	    [ OPTIONS ( option 'value' [, ... ] ) ]
	    
 [参数详解](http://www.postgres.cn/docs/10/sql-importforeignschema.html)

* 创建扩展，示例如下:
   		
   		添加扩展
	   	CREATE EXTENSION postgres_fdw;
		
		添加远程服务器
		CREATE SERVER postgres_server FOREIGN DATA WRAPPER postgres_fdw OPTIONS (host '192.168.1.150',port '5432',dbname 'wxdemo') ;
		
		添加用户
		CREATE USER MAPPING FOR postgres SERVER postgres_server OPTIONS (user 'postgres',password 'postgres');
		
		添加远程数据表
		CREATE FOREING TABLE wx_data(name character(40),age character(40)) SERVER postgres_server  OPTIONS (schema_name 'public',table_name 'wx_test');

* 查询创建，示例如下:
		
		 查看已经安装并创建的扩展工具：
	    select * from pg_foreign_data_wrapper; 
		或者\dx postgres_fdw;或者 \dx  
		
		 查看远程服务器：
	 	 select * from pg_foreign_server 或者 \des; 
	 	 
	 	 查看连接远程数据库的用户和密码：
	 	 select * from pg_user_mappings; 

	 	 查看远程表数据：
	 	 select * from wx_data ;

* 删除扩展，示例如下:
		
		删除扩展
		DROP EXTENSION postgres_fdw;

		删除服务器
		DROP server postgres_server;

		删除用户
		DROP USER MAPPING FOR postgres SERVER postgres_server;
	
		删除远程表
		 DROP FOREIGN TABLE wx_data;
		 
		如果都创建完成了，删除需要级联删除，在命令末尾加上 CASCADE，如:		 
		 DROP EXTENSION postgres_fdw  CASCADE;
		 


		


