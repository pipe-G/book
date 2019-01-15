# 错误
**1.**json.decoder.JSONDecodeError: Expecting value: line 1 column 1 (char 0)

不是json数据，得到的可能是一个地址，可以重定向。

**2.**在Mac上用Navicat连接Oracle的时候总是提示：

ORA-21561: OID generation failed

解决过程  
在终端上输入：~ hostname  
sglmac 

查看/etc/hosts文件：~ cat /etc/hosts  	
127.0.0.1 sglmac 

保持hostname与hosts文件中127.0.0.1对应的名称一致即可！ 