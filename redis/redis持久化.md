# Redis 持久化

## AOF

### 开启AOF持久化方式

```shell
# 开启aof机制
appendonly yes

# aof文件名
appendfilename "appendonly.aof"

# 写入策略,always表示每个写操作都保存到aof文件中,也可以是everysec或no
appendfsync always

# 默认不重写aof文件
no-appendfsync-on-rewrite no

# 保存目录
dir ~/redis/
```

### 三种持久化方式

```shell
appendfsync always
# 客户端的每一个写操作都保存到aof文件当，这种策略很安全，但是每个写请注都有IO操作，所以也很慢。

appendfsync everysec
# appendfsync的默认写入策略，每秒写入一次aof文件，因此，最多可能会丢失1s的数据。

appendfsync no
# Redis服务器不负责写入aof，而是交由操作系统来处理什么时候写入aof文件。更快，但也是最不安全的选择，不推荐使用。
```

### 重写方式

通过在redis.conf配置文件中的选项no-appendfsync-on-rewrite可以设置是否开启重写，这种方式会在每次fsync时都重写，影响服务器性以，因此默认值为no，不推荐使用。

```
# 默认不重写aof文件
no-appendfsync-on-rewrite no
```

客户端向服务器发送bgrewriteaof命令，也可以让服务器进行AOF重写。

```bash
# 让服务器异步重写追加aof文件命令
> bgrewriteaof
```

AOF重写方式也是异步操作，即如果要写入aof文件，则Redis主进程会forks一个子进程来处理。

重写aof文件的好处

- 压缩aof文件，减少磁盘占用量。

- 将aof的命令压缩为最小命令集，加快了数据恢复的速度。

  

### AOF文件损坏

在写入aof日志文件时，如果Redis服务器宕机，则aof日志文件文件会出格式错误，在重启Redis服务器时，Redis服务器会拒绝载入这个aof文件，可以通过以下步骤修复aof并恢复数据。

1、备份现在aof文件，以防万一。

2、使用redis-check-aof命令修复aof文件，该命令格式如下：

```
# 修复aof日志文件
$ redis-check-aof -fix file.aof
```

3、重启Redis服务器，加载已经修复的aof文件，恢复数据。

### 优缺点

优点

- AOF只是追加日志文件，因此对服务器性能影响较小，速度比RDB要快，消耗的内存较少。

缺点

- AOF方式生成的日志文件太大，即使通过AFO重写，文件体积仍然很大。



## RDB

RDB是一种快照存储持久化方式，具体就是将Redis某一时刻的内存数据保存到硬盘的文件当中，默认保存的文件名为dump.rdb，而在Redis服务器启动时，会重新加载dump.rdb文件的数据到内存当中恢复数据。

### 数据保存

```bash
# 同步数据到磁盘上
save
# 异步保存数据集到磁盘上
bgsave
```

#### 自动保存

在Redis配置文件中的save指定到达触发RDB持久化的条件，比如【多少秒内至少达到多少写操作】就开启RDB数据同步。

例如我们可以在配置文件redis.conf指定如下的选项：

```
# 900s内至少达到一条写命令
save 900 1
# 300s内至少达至10条写命令
save 300 10
# 60s内至少达到10000条写命令
save 60 10000
```

之后在启动服务器时加载配置文件。

```bash
# 启动服务器加载配置文件
redis-server redis.conf
```

**RDB的几个优点**

- 与AOF方式相比，通过rdb文件恢复数据比较快。
- rdb文件非常紧凑，适合于数据备份。
- 通过RDB进行数据备，由于使用子进程生成，所以对Redis服务器性能影响较小。

**RDB的几个缺点**

- 如果服务器宕机的话，采用RDB的方式会造成某个时段内数据的丢失，比如我们设置10分钟同步一次或5分钟达到1000次写入就同步一次，那么如果还没达到触发条件服务器就死机了，那么这个时间段的数据会丢失。
- 使用save命令会造成服务器阻塞，直接数据同步完成才能接收后续请求。
- 使用bgsave命令在forks子进程时，如果数据量太大，forks的过程也会发生阻塞，另外，forks子进程会耗费内存。