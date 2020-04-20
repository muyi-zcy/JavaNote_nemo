# Java：生产者；Python：消费者

> 交换机：direct
>
> 队列：cancel
>
> routeing key：cancel_key



## Python安装pika

采用anaconda管理python

```shell
pip install pika
```



## java发送消息

运行代码

发送消息代码如下：

```java
String id= SnowflakeIdWorker.getId().toString();
logger.info("send delay message Id:{}", id);
rabbitTemplate.convertAndSend("direct", "cancel_key",id);
```

## Python接收消息

```python
#!/usr/bin/env python
import pika

if __name__ == '__main__':

    # 账户和密码
    credentials = pika.PlainCredentials(username='guest', password='guest')
    # 服务器地址、端口号、账户和virtual_host（如果没有设置，应该会出现排他，无法接入队列）
    connection = pika.BlockingConnection(pika.ConnectionParameters('47.98.175.227', 5672, '/', credentials))
    channel = connection.channel()
    # 设置交换机和交换机类型，druable是否持久化，必须和服务端将要接入的交换机一致，否则报错
    # channel.exchange_declare(exchange='direct', exchange_type='direct', durable=True)

    # 设置监听队列参数
    channel.queue_bind(exchange='direct',
                       queue='cancel',
                       routing_key="cancel_key")
    print(' [*] Waiting for logs. To exit press CTRL+C')

    # 回调：
    def callback(ch, method, properties, body):
        # 对bytes类型转码不然会出现：d'......'
        print(body.decode())

    # 接收消息，调用回调函数，True:是否向服务器发送确认消息
    channel.basic_consume('cancel', callback, True)
    # 接着结束消息
    channel.start_consuming()
```





## 运行

发送

```java
localhost:8080/RabbitMqController/sendDirect
```



### Java端：

```shell
send delay message Id:402375211200155648
```



### Python端

```shell
 [*] Waiting for logs. To exit press CTRL+C
402375211200155648
```

