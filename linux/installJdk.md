> 版本说明: jdk1.8.0_201

## 1.下载JDK文件并上传到到服务器[华为镜像地址](https://repo.huaweicloud.com/openjdk/)
## 2.解压JDK
进入JDK存放目录,解压文件

```
[root@ym002ecs software]# tar -zxvf jdk-8u201-linux-x64.tar.gz 
```
## 3.配置JDK环境变量
```
[root@ym002ecs software]# vim /etc/profile 
```
i 进入编辑模式,在文件末尾添加


```
export JAVA_HOME=/mnt/software/jdk1.8.0_201
export CLASSPATH=$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin
export PATH JAVA_HOME CLASSPATH
```
:wq 保存并退出
运行改写过的文件

```
[root@ym002ecs software]# source  /etc/profile
```
## 4.查验JDK是否安装成功

```
[root@ym002ecs software]# java -version
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/a4aa12c990814158bd698cec683a4ddb.png#pic_center)
