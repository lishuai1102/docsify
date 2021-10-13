> 版本说明:libfastcommon-1.0.7,nginx-1.9.9,fastdfs-5.05,fastdfs-nginx-module-1.16
> 路径说明: storage日志和数据目录/home/fastdfs/storage  tracker日志目录:/home/fastdfs/tracker

# 1.下载所需安装包
```
wget https://github.com/happyfish100/libfastcommon/archive/V1.0.7.tar.gz
wget http://jaist.dl.sourceforge.net/project/fastdfs/FastDFS%20Nginx%20Module%20Source%20Code/fastdfs-nginx-module_v1.16.tar.gz
wget https://github.com/happyfish100/fastdfs/archive/V5.05.tar.gz
wget http://nginx.org/download/nginx-1.12.1.tar.gz
```
# 2.安装gcc
```
yum -y install gcc gcc-c++ autoconf pcre pcre-devel make automake
yum -y install wget httpd-tools vim
```
# 3.安装libfastcommon
进入libfastcommon压缩包目录解压，编译，安装
```
[root@do0805 local]#  V1.0.7.tar.gz
```
进入解压的libfastcommon-1.0.7目录，编译
```
[root@do0805 local]#  ./make.sh
```
#### 3.3 安装
```
[root@do0805 local]#  ./make.sh install
```
将libfastcommon.so文件复制到usr/lib下
```
[root@do0805 local]#  cp /usr/local/libfastcommon-1.0.7/src/libfastcommon.so /usr/lib
```
# 4.安装 FastDFS
解压V5.05.tar.gz，然后编译安装FastDFS
~~~
[root@do0805 local]#  tar -zxvf V5.05.tar.gz
[root@do0805 local]#  ./make.sh
~~~

~~~
[root@do0805 local]#  ./make.sh install
~~~
安装成功后将目录conf内的文件拷贝到/etc/fdfs目录下：
~~~
[root@do0805 local]# cp /usr/local/fastdfs-5.05/conf/* /etc/fdfs/
~~~
# 5.安装tracker
进入/etc/fdfs目录，修改tracker.conf文件
~~~
base_path=/home/fastdfs/tracker 

http.server_port=8080

store_group=group1 
~~~
 启动tracker，并查看是启动成功
```
[root@do0805 fdfs]#  fdfs_trackerd /etc/fdfs/tracker.conf start
[root@do0805 fdfs]#  ps -ef|grep tracker
```
# 6.安装storage
修改storage.conf文件
~~~
base_path=/home/fastdfs/storage   

store_path0=/home/fastdfs/storage  

tracker_server= ip:22122 

group_name=group1 

http.server_port=8888 
~~~
 启动storage，并查看是否成功
~~~
[root@do0805 fdfs]#  fdfs_storaged /etc/fdfs/storage.conf start
[root@do0805 fdfs]#  ps -ef|grep storage
~~~
测试tracker和storage之间的通信
~~~
[root@do0805 fdfs]# fdfs_monitor /etc/fdfs/storage.conf
~~~
出现以下内容,则表示成功![在这里插入图片描述](https://img-blog.csdnimg.cn/39ce954218094298bfc272ec900915d0.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIwNDQ1NA==,size_16,color_FFFFFF,t_70#pic_center)
# 7.测试图片上传
 修改/etc/fdfs下client.conf
~~~
base_path=/home/fastdfs/client
tracker_server=ip:22122
~~~
 测试命令,返回地址则表示成功
~~~
[root@do0805 fdfs]#  /usr/bin/fdfs_test /etc/fdfs/client.conf upload 1.jpg
~~~
# 8.安装Nginx
~~~
[root@do0805 local]#  tar -zxvf  nginx-1.9.9.tar.gz
[root@do0805 local]#  cd nginx-1.9.9
[root@do0805 local]#  ./configure
[root@do0805 local]#  make
[root@do0805 local]#  make install
~~~
如果出现报错,请尝试以下解决方案:
1.进入/usr/local/nginx-1.9.9/objs,修改Makefile文件
将CFLAGS =  -pipe  -O -W -Wall -Wpointer-arith -Wno-unused -Werror  -g 中的-Werror删除
2.进入/usr/local/nginx-1.9.9/src/os/unix,修改ngx_user.c文件
将cd.current_salt[0] = ~salt[0];  删除  
重新执行 ./make.sh
# 9.安装fastdfs-nginx-module 
~~~
[root@do0805 local]#  tar -zxvf fastdfs-nginx-module_v1.16.tar.gz
~~~
拷贝usr/lib64目录下库文件libfdfsclient.so 
~~~
[root@do0805 local]#  cp /usr/lib64/libfdfsclient.so /usr/lib 
~~~
修改/usr/local/fastdfs-nginx-module/src/config文件。去掉所有的local
在Nginx安装目录将fastdfs模块添加到Nginx
~~~
[root@do0805 nginx-1.9.9]# ./configure --add-module=../fastdfs-nginx-module/src
~~~
重新安装编译
~~~
[root@do0805 nginx-1.9.9]# make && make install
~~~
查看Nginx的模块
~~~
[root@do0805 nginx-1.9.9]# /usr/local/nginx/sbin/nginx -V
~~~
复制 fastdfs-nginx-module 源码中的配置文件到/etc/fdfs 目录， 并修改
~~~
[root@do0805 nginx-1.9.9]#  cp /usr/local/fastdfs-nginx-module/src/mod_fastdfs.conf /etc/fdfs/
~~~
修改mod_fastdfs.conf配置
~~~
tracker_server=ip:22122
storage_server_port=23000
url_have_group_name = true
store_path0=/home/fastdfs/storage
store_path0=/home/fastdfs/storage
~~~
进入Nginx编译目录,修改nginx.conf,添加server
~~~
    server {
        listen       8888;
        server_name  localhost;

        location ~/group([0-9])/M00 {
          ngx_fastdfs_module;
       }
    }
~~~
在文件存储目录下创建软连接，将其链接到实际存放数据的目录，注意这个文件存储的位置后面多个data目录是系统自动生成的
~~~
ln -s /home/fastdfs/storage/data /home/fastdfs/storage/data/M00
~~~
启动nginx
~~~
/usr/local/nginx/sbin/nginx
~~~
