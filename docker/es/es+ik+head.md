# linux docker安装Elarsticsearch IK分词器 Head插件

# Elarsticsearch

### 下载Elarsticsearch

```shell
docker pull elacticsearch
```

### 主机新建配置文件

```shell
cd /root/esconfig
vim elacticsearch.yml
```

### elacticsearch.yml内容:

```shell
http.host: 0.0.0.0

# Uncomment the following lines for a production cluster deployment
transport.host: 0.0.0.0
#discovery.zen.minimum_master_nodes: 1

```

### 启动：

```shell
docker run -it -p 9200:9200 -p 9300:9300 -v /root/esconfig/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml elasticsearch
```

### 出现问题：

```shell
error='Cannot allocate memory' (errno=12)
```

由于elasticsearch5.0默认分配jvm空间大小为2g，需要改小一点

```shell
//找到配置文件
cd /var/lib/docker/overlay2

find / -name jvm.options
//输出
/var/lib/docker/overlay2/d2815d837487d6398717e26b11e96afb197dbb23061f139cc9b61a67014c2e06/diff/etc/elasticsearch/jvm.options

cd/var/lib/docker/overlay2/d2815d837487d6398717e26b11e96afb197dbb23061f139cc9b61a67014c2e06/diff/etc/elasticsearch/

vim jvm.options  
-Xms2g  →  -Xms512m
-Xmx2g  →  -Xmx512m
```



```shell
max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

先要切换到root用户；
 然后可以执行以下命令，设置 vm.max_map_count ，但是重启后又会恢复为原值。

```shell
sysctl -w vm.max_map_count=262144
```

持久性的做法是在 /etc/sysctl.conf 文件中修改 vm.max_map_count 参数：

```bash
echo "vm.max_map_count=262144" > /etc/sysctl.conf
sysctl -p
```

现在就可以访问了:发送put请求：localhost:9200





## IK分词器

进入elasticsearch

```shell
docker exec -it elasticsearch bash
```

进入plugin

```shell
cd plugin
```

下载资源

```shell
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v5.6.12/elasticsearch-analysis-ik-5.6.12.zip
```

注意下载得版本与elasticsearch版本相同，查看elasticsearch版本：

```shell
docker images
查看TAG列版本号
```

但是显示latest，前往https://hub.docker.com/_/elasticsearch查看最新版本elasticsearch版本号



下载完成以后解压，重命名：

```shell
unzip elasticsearch-analysis-ik-5.6.12.zip

mv elasticsearch ik

//最重要得记得删除压缩包,不然无法启动
rm elasticsearch-analysis-ik-5.6.12.zip
```



## Head插件

下载

```shell
 docker pull mobz/elasticsearch-head:5
```

启动

```shell
docker run -it --name="es-admin" -p 9100:9100 mobz/elasticsearch-head:5
```

此时访问localhost:9100

但是无法连接localhost:9200，即显示：集群健康值：未连接



因为没有设置跨域，修改配置文件elasticsearch.yml

```shell
http.host: 0.0.0.0

# Uncomment the following lines for a production cluster deployment
transport.host: 0.0.0.0
#discovery.zen.minimum_master_nodes: 1
http.cors.enabled: true
http.cors.allow-origin: "*"
```

重启elasticsearch即可