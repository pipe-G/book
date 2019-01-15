#Method

##pip 更换源
###mac
* 进入根目录：cd ~/
* 进入.pip目录  cd .pip
* 如果不存在文件夹就新建mkdir .pip
* 进入 cd .pip
* 创建pip.conf文件 touch pip.conf
* 修改：vim pip.conf

暂时使用:
	
	pip install -i https://pypi.douban.com/simple
	
永久修改
	
	[global]
	index-url=http://mirrors.aliyun.com/pypi/simple/

现在最新的pip要求源必须是https的，不然会警告，所以需要添加,也可以不加:
		
	[install]
	trusted-host=mirrors.aliyun.com

所以完整的为:
		
	[global]
	index-url=http://mirrors.aliyun.com/pypi/simple/
	[install]
	trusted-host=mirrors.aliyun.com

国内的源:

	http://pypi.douban.com/  豆瓣
	https://pypi.tuna.tsinghua.edu.cn/simple/ 清华大学  
	
	http://pypi.hustunique.com/  华中理工大学
	
	http://pypi.sdutlinux.org/  山东理工大学
	
	http://pypi.mirrors.ustc.edu.cn/  中国科学技术大学

###windows

>直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下

	[global]
	index-url = https://pypi.tuna.tsinghua.edu.cn/simple