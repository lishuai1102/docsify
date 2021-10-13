> 版本说明:Nginx-1.9.9

# 1.下载JDK文件并上传到到服务器[华为镜像地址](https://repo.huaweicloud.com/nginx/)
# 2.安装gcc-c++编译器
```
[root@ym002ecs ~]# yum install gcc-c++
[root@ym002ecs ~]# yum install -y openssl openssl-devel
```
# 3.安装pcre包
```
[root@ym002ecs ~]# yum install -y pcre pcre-devel
```
# 4.解压Nginx,进入Nginx目录
```
[root@ym002ecs nginx]# tar -zxvf nginx-1.9.9.tar.gz 
[root@ym002ecs nginx]# cd nginx-1.9.9
```
# 5.配置文件目录和二进制脚本目录

```
[root@ym002ecs nginx-1.9.9]#  ./configure --prefix=/opt/nginx --sbin-path=/usr/bin/nginx
```
# 6.编译安装
```
[root@ym002ecs nginx-1.9.9]# make
[root@ym002ecs nginx-1.9.9]# make install
```
# 7.启动Nginx
```
[root@ym002ecs nginx-1.9.9]# nginx
```
测试是否安装成功![在这里插入图片描述](https://img-blog.csdnimg.cn/04ebf0be6af54292aaf4ead62b124610.png)