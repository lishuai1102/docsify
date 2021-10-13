> 版本说明:Redis-6.0.0


# 1.下载安装包并上传到到服务器[华为镜像地址](https://repo.huaweicloud.com/redis/)
# 2.解压

```bash
[root@ym002ecs local]# tar -zvxf redis-6.0.0.tar.gz 
```
# 3.编译

```bash
[root@ym002ecs local]# cd /usr/local/redis
[root@ym002ecs redis]# make
```
如果出现以下错误,可能是由于GCC版本过低造成,
![在这里插入图片描述](https://img-blog.csdnimg.cn/dace6a493d5143e49b1737a87a352832.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIwNDQ1NA==,size_16,color_FFFFFF,t_70#pic_center)
升级GCC版本

```bash
[root@ym002ecs ~]# yum -y install centos-release-scl
[root@ym002ecs ~]# yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
[root@ym002ecs ~]# scl enable devtoolset-9 bash
[root@ym002ecs ~]# echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile
```
再次执行make
![在这里插入图片描述](https://img-blog.csdnimg.cn/0deebcb801054784a4cc4d0402a01e6c.png)
# 3.安装

```bash
[root@ym002ecs redis]# make PREFIX=/usr/local/redis install
[root@ym002ecs redis]# make  install
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/1ba26aa2115a4d249c5e8e8b6f6cce16.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIwNDQ1NA==,size_16,color_FFFFFF,t_70)
# 4.启动redis
```bash
[root@ym002ecs redis]# ./bin/redis-server& ./redis.conf
```
# 5.测试连接
```bash
[root@ym002ecs redis]# redis-cli
```