#Nginx

编译:
./configure  --prefix=/opt/nginx/nginx-1.14.2/

centos6下编译可能会缺少模块，这是我缺少的模块：

yum install pcre pcre-devel -y

yum install -y zlib-devel