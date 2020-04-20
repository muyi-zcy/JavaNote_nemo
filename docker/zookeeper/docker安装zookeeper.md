# Docker zookeeper

## docker 安装zookeeper

### 下载镜像

```shell
docker pull zookeeper
```

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200414130925.png)

选择官方的镜像下载：

```shell
docker pull zookeeper
```

### 启动容器并添加映射

```shell
docker run --privileged=true -d --name zookeeper --publish 2181:2181  -d zookeeper:latest
```

### 查看容器是否启动

```shell
docker ps
```





## IDEA安装zookeeper插件

​	idea->setting->plugin->zoookeeper搜索安装：

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200414131400.png)

安装以后重启idea;



#### 连接zookeeper

配置Zookeeper的连接信息

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200414131605.png)

连接成功：

<img src="https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200414131732.png" style="zoom:80%;" />



