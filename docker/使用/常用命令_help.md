# Docker常用命令

## 帮助命令

docker 详细信息

```shell
docker info
```

命令查看
```shell
docker --help
```
```shell
[root@localhost ~]# docker --help

Usage:	docker [OPTIONS] COMMAND //公式

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/root/.docker")
  -c, --context string     Name of the context to use to connect to the daemon (overrides DOCKER_HOST env var and default context set with "docker context use")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/root/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/root/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/root/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  builder     Manage builds
  config      Manage Docker configs
  container   Manage containers
  context     Manage contexts
  engine      Manage the docker engine
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  secret      Manage Docker secrets
  service     Manage services
  stack       Manage Docker stacks
  swarm       Manage Swarm
  system      Manage Docker
  trust       Manage trust on Docker images
  volume      Manage volumes

Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

Run 'docker COMMAND --help' for more information on a command.

```



## 命令对象与格式

对于 Compose 来说，大部分命令的对象既可以是项目本身，也可以指定为项目中的服务或者容器。如果没有特别的说明，命令对象将是项目，这意味着项目中所有的服务都会受到命令影响。

执行 `docker-compose [COMMAND] --help` 或者 `docker-compose help [COMMAND]` 可以查看具体某个命令的使用格式。

`docker-compose` 命令的基本的使用格式是

```bash
docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]
```

## 命令选项

* `-f, --file FILE` 指定使用的 Compose 模板文件，默认为 `docker-compose.yml`，可以多次指定。

* `-p, --project-name NAME` 指定项目名称，默认将使用所在目录名称作为项目名。

* `--x-networking` 使用 Docker 的可拔插网络后端特性

* `--x-network-driver DRIVER` 指定网络后端的驱动，默认为 `bridge`

* `--verbose` 输出更多调试信息。

* `-v, --version` 打印版本并退出。

## 命令使用说明

### `build`

格式为 `docker-compose build [options] [SERVICE...]`。

构建（重新构建）项目中的服务容器。

服务容器一旦构建后，将会带上一个标记名，例如对于 web 项目中的一个 db 容器，可能是 web_db。

可以随时在项目目录下运行 `docker-compose build` 来重新构建服务。

选项包括：

* `--force-rm` 删除构建过程中的临时容器。

* `--no-cache` 构建镜像过程中不使用 cache（这将加长构建过程）。

* `--pull` 始终尝试通过 pull 来获取更新版本的镜像。

### `config`

验证 Compose 文件格式是否正确，若正确则显示配置，若格式错误显示错误原因。

### `down`

此命令将会停止 `up` 命令所启动的容器，并移除网络

### `exec`

进入指定的容器。

### `help`

获得一个命令的帮助。

### `images`

列出 Compose 文件中包含的镜像。

### `kill`

格式为 `docker-compose kill [options] [SERVICE...]`。

通过发送 `SIGKILL` 信号来强制停止服务容器。

支持通过 `-s` 参数来指定发送的信号，例如通过如下指令发送 `SIGINT` 信号。

```bash
$ docker-compose kill -s SIGINT
```

### `logs`

格式为 `docker-compose logs [options] [SERVICE...]`。

查看服务容器的输出。默认情况下，docker-compose 将对不同的服务输出使用不同的颜色来区分。可以通过 `--no-color` 来关闭颜色。

该命令在调试问题的时候十分有用。

### `pause`

格式为 `docker-compose pause [SERVICE...]`。

暂停一个服务容器。

### `port`

格式为 `docker-compose port [options] SERVICE PRIVATE_PORT`。

打印某个容器端口所映射的公共端口。

选项：

* `--protocol=proto` 指定端口协议，tcp（默认值）或者 udp。

* `--index=index` 如果同一服务存在多个容器，指定命令对象容器的序号（默认为 1）。

### `ps`

格式为 `docker-compose ps [options] [SERVICE...]`。

列出项目中目前的所有容器。

选项：

* `-q` 只打印容器的 ID 信息。

### `pull`

格式为 `docker-compose pull [options] [SERVICE...]`。

拉取服务依赖的镜像。

选项：

* `--ignore-pull-failures` 忽略拉取镜像过程中的错误。

### `push`

推送服务依赖的镜像到 Docker 镜像仓库。

### `restart`

格式为 `docker-compose restart [options] [SERVICE...]`。

重启项目中的服务。

选项：

* `-t, --timeout TIMEOUT` 指定重启前停止容器的超时（默认为 10 秒）。

### `rm`

格式为 `docker-compose rm [options] [SERVICE...]`。

删除所有（停止状态的）服务容器。推荐先执行 `docker-compose stop` 命令来停止容器。

选项：

* `-f, --force` 强制直接删除，包括非停止状态的容器。一般尽量不要使用该选项。

* `-v` 删除容器所挂载的数据卷。

### `run`
格式为 `docker-compose run [options] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]`。

在指定服务上执行一个命令。

例如：

```bash
$ docker-compose run ubuntu ping docker.com
```

将会启动一个 ubuntu 服务容器，并执行 `ping docker.com` 命令。

默认情况下，如果存在关联，则所有关联的服务将会自动被启动，除非这些服务已经在运行中。

该命令类似启动容器后运行指定的命令，相关卷、链接等等都将会按照配置自动创建。

两个不同点：

* 给定命令将会覆盖原有的自动运行命令；

* 不会自动创建端口，以避免冲突。

如果不希望自动启动关联的容器，可以使用 `--no-deps` 选项，例如

```bash
$ docker-compose run --no-deps web python manage.py shell
```

将不会启动 web 容器所关联的其它容器。

选项：

* `-d` 后台运行容器。

* `--name NAME` 为容器指定一个名字。

* `--entrypoint CMD` 覆盖默认的容器启动指令。

* `-e KEY=VAL` 设置环境变量值，可多次使用选项来设置多个环境变量。

* `-u, --user=""` 指定运行容器的用户名或者 uid。

* `--no-deps` 不自动启动关联的服务容器。

* `--rm` 运行命令后自动删除容器，`d` 模式下将忽略。

* `-p, --publish=[]` 映射容器端口到本地主机。

* `--service-ports` 配置服务端口并映射到本地主机。

* `-T` 不分配伪 tty，意味着依赖 tty 的指令将无法运行。

### `scale`

格式为 `docker-compose scale [options] [SERVICE=NUM...]`。

设置指定服务运行的容器个数。

通过 `service=num` 的参数来设置数量。例如：

```bash
$ docker-compose scale web=3 db=2
```

将启动 3 个容器运行 web 服务，2 个容器运行 db 服务。

一般的，当指定数目多于该服务当前实际运行容器，将新创建并启动容器；反之，将停止容器。

选项：

* `-t, --timeout TIMEOUT` 停止容器时候的超时（默认为 10 秒）。

### `start`

格式为 `docker-compose start [SERVICE...]`。

启动已经存在的服务容器。

### `stop`

格式为 `docker-compose stop [options] [SERVICE...]`。

停止已经处于运行状态的容器，但不删除它。通过 `docker-compose start` 可以再次启动这些容器。

选项：

* `-t, --timeout TIMEOUT` 停止容器时候的超时（默认为 10 秒）。

### `top`

查看各个服务容器内运行的进程。

### `unpause`

格式为 `docker-compose unpause [SERVICE...]`。

恢复处于暂停状态中的服务。

### `up`

格式为 `docker-compose up [options] [SERVICE...]`。

该命令十分强大，它将尝试自动完成包括构建镜像，（重新）创建服务，启动服务，并关联服务相关容器的一系列操作。

链接的服务都将会被自动启动，除非已经处于运行状态。

可以说，大部分时候都可以直接通过该命令来启动一个项目。

默认情况，`docker-compose up` 启动的容器都在前台，控制台将会同时打印所有容器的输出信息，可以很方便进行调试。

当通过 `Ctrl-C` 停止命令时，所有容器将会停止。

如果使用 `docker-compose up -d`，将会在后台启动并运行所有的容器。一般推荐生产环境下使用该选项。

默认情况，如果服务容器已经存在，`docker-compose up` 将会尝试停止容器，然后重新创建（保持使用 `volumes-from` 挂载的卷），以保证新启动的服务匹配 `docker-compose.yml` 文件的最新内容。如果用户不希望容器被停止并重新创建，可以使用 `docker-compose up --no-recreate`。这样将只会启动处于停止状态的容器，而忽略已经运行的服务。如果用户只想重新部署某个服务，可以使用 `docker-compose up --no-deps -d <SERVICE_NAME>` 来重新创建服务并后台停止旧服务，启动新服务，并不会影响到其所依赖的服务。

选项：

* `-d` 在后台运行服务容器。

* `--no-color` 不使用颜色来区分不同的服务的控制台输出。

* `--no-deps` 不启动服务所链接的容器。

* `--force-recreate` 强制重新创建容器，不能与 `--no-recreate` 同时使用。

* `--no-recreate` 如果容器已经存在了，则不重新创建，不能与 `--force-recreate` 同时使用。

* `--no-build` 不自动构建缺失的服务镜像。

* `-t, --timeout TIMEOUT` 停止容器时候的超时（默认为 10 秒）。

### `version`

格式为 `docker-compose version`。

打印版本信息。

## 参考资料

* [官方文档](https://docs.docker.com/compose/reference/overview/)

