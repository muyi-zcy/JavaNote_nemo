# Linux Docker 安装RocketMQ



### 安装 Namesrv

```shell
docker run -d -p 9876:9876 -v /root/rocketmqdata/data/namesrv/logs:/root/logs -v /root/rocketmqdata/data/namesrv/store:/root/store --name rmqnamesrv -e "MAX_POSSIBLE_HEAP=100000000" rocketmqinc/rocketmq sh mqnamesrv
```
宿主机想保存 MQ 的日志与数据的地方，通过 docker 的 -v 参数使用 volume 功能，把你本地的目录映射到容器内的目录上。否则所有数据都默认保存在容器运行时的内存中，重启之后就又回到最初的起点。

### 安装 broker 服务器

#### 创建 broker.conf 文件

1. 在 /root/rocketmqdata/conf 目录下创建 broker.conf 文件
2. 在 broker.conf 中写入如下内容

```
brokerClusterName = DefaultCluster
brokerName = broker-a
brokerId = 0
deleteWhen = 04
fileReservedTime = 48
brokerRole = ASYNC_MASTER
flushDiskType = ASYNC_FLUSH
brokerIP1 = {本地外网 IP}
```

**brokerIP1 要修改成你自己宿主机的 IP**

启动容器

```shell
docker run -d -p 10911:10911 -p 10909:10909 -v  /root/rocketmqdata/data/broker/logs:/root/logs -v  /root/rocketmqdata/rocketmq/data/broker/store:/root/store -v  /root/rocketmqdata/conf/broker.conf:/opt/rocketmq/conf/broker.conf --name rmqbroker --link rmqnamesrv:namesrv -e "NAMESRV_ADDR=namesrv:9876" -e "MAX_POSSIBLE_HEAP=200000000" rocketmqinc/rocketmq sh mqbroker -c /opt/rocketmq/conf/broker.conf
```

### 安装styletang/rocketmq-console-ng：rocketmq 控制台

拉取镜像：

```shell
docker pull styletang/rocketmq-console-ng
```

启动


```shell
docker run -e "JAVA_OPTS=-Drocketmq.namesrv.addr=47.98.175.227:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false" -p 8081:8080 -t styletang/rocketmq-console-ng
```

### 完成

浏览器输入：

```url
http://47.98.175.227:8080/
```

