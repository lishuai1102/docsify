> 版本说明:mysql-8.0.24

## 1.下载mysql安装包,上传至服务器[华为镜像地址](https://repo.huaweicloud.com/mysql/Downloads/)
## 2. 解压mysql
因下载的文件为.xz,若为.tar可跳过第一步
```
[root@ym002ecs software]# xz -d mysql-8.0.24-linux-glibc2.12-x86_64.tar.xz 
```
```
[root@ym002ecs software]# tar xvf mysql-8.0.24-linux-glibc2.12-x86_64.tar 
```
修改文件名
```
[root@ym002ecs software]# mv mysql-8.0.24-linux-glibc2.12-x86_64 mysql
```
## 3.配置
###  3.1 创建用户和用户组
```
[root@ym002ecs software]# groupadd mysql
[root@ym002ecs software]# useradd -r -g mysql mysql
```
### 3.2修改权限
保证安装MySQL目录的所有者为mysql用户，所属组为mysql组

```
[root@ym002ecs software]# chown -R mysql .
[root@ym002ecs software]# chgrp -R mysql .
```
### 3.3 初始化数据库
进入mysql目录bin目录下执行
```
[root@ym002ecs bin]# ./mysqld --initialize --user=mysql --basedir=/mnt/software/mysql --datadir=/mnt/data/mysql/data
```
若出现以下错误
```bash
./mysqld: error while loading shared libraries: libaio.so.1: 
cannot open shared object file: No such file or directory
```
是因为没有安装libaio,执行以下命令
```
[root@ym002ecs mysql]# yum install -y libaio

根据对应错误提示可尝试安装以下类库:
yum install libaio
yum install libaio.so.1

yum -y install numactl
yum install libnuma
yum install libnuma.so.1
yum install ld-linux.so.2
yum install libstdc++.so.6
yum install libtinfo.so.5
yum clean all
yum makecache

```
重新执行初始化命令,并记录初始密码
### 3.4生成配置文件
```
[root@ym002ecs bin]# vim /etc/my.cnf
```
在文件中插入

```bash
[mysqld]
    basedir = /mnt/software/mysql   
    datadir = /mnt/data/mysql/data
    socket = /mnt/software/mysql/mysql.sock
    port = 3306
   sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
 [client]
   socket = /mnt/software/mysql/mysql.sock
```
### 3.5配置MySQL服务
```
[root@ym002ecs bin]# cp  /mnt/software/mysql/support-files/mysql.server /etc/init.d/mysql
[root@ym002ecs bin]# chmod +x /etc/init.d/mysql
[root@ym002ecs bin]# chkconfig --add mysql
```
### 3.6配置环境变量
在/etc/profile文件最后添加以下内容
```bash
export MYSQL_HOME=/mnt/software/mysql
export PATH=$PATH:$MYSQL_HOME/bin:$MYSQL_HOME/lib
```
使环境变量生效
```
source /etc/profile
```
## 4.启动mysql
```
[root@ym002ecs bin]# service mysql start
```
## 5.更改初始密码
输入初始密码,登录mysql
```
mysql -u root -p
```
```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED with mysql_native_password BY '你的密码';
```
更改初始密码
## 6.打开远程服务

```
mysql> use mysql
mysql> update user set user.Host='%' where user.User='root';
mysql> flush privileges;
```
## 7. 配置阿里云安全组规则
## 8.Navicat 测试远程连接

注意:
若出现以下错误:2059 - Authentication plugin 'caching_sha2_password' cannot be loaded:![在这里插入图片描述](https://img-blog.csdnimg.cn/f261eeff7c0041f087178d7e86de99c4.png#pic_center)
 以上报错是由于目前已有的客户端连接软件还不支持Mysql8新增加的加密方式caching_sha2_password，所以我们需要修改用户的加密方式，将其改为老的加密验证方式。
进入mysql 数据库执行
```
mysql> alter user 'root'@'%' identified with mysql_native_password by 'do0805@.';
mysql> flush privileges;
```
重新尝试连接
