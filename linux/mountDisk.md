> 阿里云服务器云盘默认不会自动挂载,需要手动配置挂载
# 1.查看SSD云盘
```
[root@ym002ecs]: fdisk -l
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/14957fe813814ba0b1b9810a9d2f2726.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIwNDQ1NA==,size_16,color_FFFFFF,t_70#pic_center)


可以看到SSD系统已经识别为/dev/vdb
# 2.格式化云盘

```
[root@ym002ecs]: mkfs.ext4 /dev/vdb
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/48cd6a5297d84fcc988e2a97c506752f.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIwNDQ1NA==,size_16,color_FFFFFF,t_70#pic_center)
# 3.将云盘挂载到mnt目录下
```
[root@ym002ecs]: mount /dev/vdb  /mnt
```
# 4.查看是否挂载成功
```
[root@ym002ecs]: df -h
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/dbbaaff0f298431e9b26374ddf64f204.png#pic_center)
# 5.配置开机自动挂载
修改/etc/fstab文件，文件末尾添加：
```
/dev/vdb   /opt ext4    defaults    0  0 
```
