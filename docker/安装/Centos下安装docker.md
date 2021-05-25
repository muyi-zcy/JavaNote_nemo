# Centos7下安装Docker Engine-Community

## 系统环境

```shell
[root@izbp1ikb8hczg6u9pf0mxvz ~]# cat /etc/redhat-release
CentOS Linux release 7.3.1611 (Core) 
[root@izbp1ikb8hczg6u9pf0mxvz ~]# cat /proc/version
Linux version 3.10.0-514.26.2.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-11) (GCC) ) #1 SMP Tue Jul 4 15:04:05 UTC 2017
```

## 更新yum

因为之前安装遇到过因为环境问题无法启动的情况，所以安装前先把系统内所有包升级

```shell
yum update
```
## 卸载旧版本

Docker使用EPEL发布，RHEL系的OS首先要确保已经持有EPEL仓库，否则先检查OS的版本，然后安装相应的EPEL包。

```shell
[root@izbp1ikb8hczg6u9pf0mxvz ~]# sudo yum remove docker \
>                   docker-client \
>                   docker-client-latest \
>                   docker-common \
>                   docker-latest \
>                   docker-latest-logrotate \
>                   docker-logrotate \
>                   docker-engine
Loaded plugins: fastestmirror
No Match for argument: docker
No Match for argument: docker-client
No Match for argument: docker-client-latest
No Match for argument: docker-common
No Match for argument: docker-latest
No Match for argument: docker-latest-logrotate
No Match for argument: docker-logrotate
No Match for argument: docker-engine
No Packages marked for removal
[root@izbp1ikb8hczg6u9pf0mxvz ~]# 

```

## 安装

### 使用存储库安装

在新主机上首次安装Docker Engine-Community之前，需要设置Docker存储库。之后，您可以从存储库安装和更新Docker。

#### 设置存储库

1. 安装所需的软件包。`yum-utils`提供了`yum-config-manager` 效用，并`device-mapper-persistent-data`和`lvm2`由需要 `devicemapper`存储驱动程序。

   ```shell
   sudo yum install -y yum-utils \
     device-mapper-persistent-data \
     lvm2
   ```

   最后出现complete即成功。

2. 使用以下命令来设置**稳定的**存储库。

   ```shell
   $ sudo yum-config-manager \
       --add-repo \
       https://download.docker.com/linux/centos/docker-ce.repo
   ```

#### 安装DOCKER ENGINE-社区

##### 安装最新版本的Docker Engine-Community和containerd

```shell
$ sudo yum install docker-ce docker-ce-cli containerd.io
```

如果提示您接受GPG密钥，请验证指纹是否匹配 `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35`，如果是，则接受它。

##### 安装特定版本的Docker Engine-Community

请在存储库中列出可用版本，然后选择并安装：

列出并排序您存储库中可用的版本。此示例按版本号（从高到低）对结果进行排序:

```shell
$ yum list docker-ce --showduplicates | sort -r
//回车
docker-ce.x86_64  3:18.09.1-3.el7                     docker-ce-stable
docker-ce.x86_64  3:18.09.0-3.el7                     docker-ce-stable
docker-ce.x86_64  18.06.1.ce-3.el7                    docker-ce-stable
docker-ce.x86_64  18.06.0.ce-3.el7                    docker-ce-stable
```

通过其完全合格的软件包名称安装特定版本，该软件包名称是软件包名称（`docker-ce`）加上版本字符串（第二列），从第一个冒号（`:`）一直到第一个连字符，并用连字符（`-`）分隔。例如，`docker-ce-18.09.1`。

```shell
$ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

#### 启动Docker

```shell
$ sudo systemctl start docker
```

通过运行`hello-world` 映像来验证是否正确安装了Docker Engine-Community 。

```shell
$ sudo docker run hello-world
```

此命令下载测试镜像并在容器中运行它。容器运行时，它会打印参考消息并退出。

![](https://raw.githubusercontent.com/pomole/nemo_pic/master/20200215021125.png)

## 配置阿里云镜像加速器

### 安装校验，查看docker 版本信息

```shell
[root@izbp1ikb8hczg6u9pf0mxvz ~]# docker version
Client: Docker Engine - Community
 Version:           19.03.6
 API version:       1.40
 Go version:        go1.12.16
 Git commit:        369ce74a3c
 Built:             Thu Feb 13 01:29:29 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.6
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.16
  Git commit:       369ce74a3c
  Built:            Thu Feb 13 01:28:07 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.10
  GitCommit:        b34a5c8af56e510852c35414db4c1f4fa6172339
 runc:
  Version:          1.0.0-rc8+dev
  GitCommit:        3e425f80a8c931f88e6d94a8c831b9d5aa481657
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683

```

针对Docker客户端版本大于 1.10.0 的用户

可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```shell
[root@izbp1ikb8hczg6u9pf0mxvz ~]# cd /etc/docker/
[root@izbp1ikb8hczg6u9pf0mxvz docker]# ls
key.json
[root@izbp1ikb8hczg6u9pf0mxvz docker]# sudo tee /etc/docker/daemon.json <<-'EOF'
{
   "registry-mirrors": ["https://w151o9g2.mirror.aliyuncs.com"]
}
 EOF
//回车
{
  "registry-mirrors": ["https://w151o9g2.mirror.aliyuncs.com"]
}
[root@izbp1ikb8hczg6u9pf0mxvz docker]# ls
daemon.json  key.json
[root@izbp1ikb8hczg6u9pf0mxvz docker]# cat daemon.json 
{
  "registry-mirrors": ["https://w151o9g2.mirror.aliyuncs.com"]
}
```
### 加载服务配置文件

```shell
sudo systemctl daemon-reload 
```
### 重启服务器

```shell
[root@izbp1ikb8hczg6u9pf0mxvz docker]# sudo service docker restart
Redirecting to /bin/systemctl restart docker.service
```

### 检测是否生效

 查看：Registry Mirrors

```shell
[root@izbp1ikb8hczg6u9pf0mxvz docker]# docker info
Client:
 Debug Mode: false

Server:
 Containers: 1
  Running: 0
  Paused: 0
  Stopped: 1
 Images: 1
 Server Version: 19.03.6
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: b34a5c8af56e510852c35414db4c1f4fa6172339
 runc version: 3e425f80a8c931f88e6d94a8c831b9d5aa481657
 init version: fec3683
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 3.10.0-514.26.2.el7.x86_64
 Operating System: CentOS Linux 7 (Core)
 OSType: linux
 Architecture: x86_64
 CPUs: 1
 Total Memory: 1.796GiB
 Name: izbp1ikb8hczg6u9pf0mxvz
 ID: JSLN:C653:6E4T:WQUE:JGJS:WMKB:OZ7E:NUVU:55KL:53VP:IEGB:J4YC
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Registry Mirrors:
  https://w151o9g2.mirror.aliyuncs.com/
 Live Restore Enabled: false

```

