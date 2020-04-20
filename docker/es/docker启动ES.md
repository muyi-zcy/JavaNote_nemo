# docker启动elasticsearch失败--jvm内存不足解决方案

centos下载完elasticsearch并修改完配置后运行docker命令：

docker run –name es1 -p 9200:9200 -p 9300:9300 -d -v
/docker/es/esmaster/es.yml:/usr/share/elasticsearch/config/elasticsearch.yml
-v /docker/es/esmaster/data:/usr/share/elasticsearch/data elasticsearch

发现没有启动成功，去除上面命令的-d后打印错误如下

Java HotSpot(TM) 64-Bit Server VM warning: INFO:
os::commit_memory(0x0000000085330000, 2060255232, 0) failed;
error=’Cannot allocate memory’ (errno=12)

经过一番查找发现这是由于elasticsearch5.0默认分配jvm空间大小为2g，内存不足以分配导致。

解决方法就是修改jvm空间分配
运行命令：

find /var/lib/docker/overlay/ -name jvm.options


查找jvm.options文件，找到后进入使用vi命令打开jvm.options如下：

将

-Xms2g  
-Xmx2g
2
修改为

-Xms512m  
-Xmx512m 
2
保存退出即可。再次运行创建运行elasticsearch命令，成功启动。



![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200309015226.png)