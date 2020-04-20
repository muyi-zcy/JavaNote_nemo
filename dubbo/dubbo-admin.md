# dubbo-admin

```
docker run -d \
-p 8080:8080 \
-e dubbo.registry.address=zookeeper://192.168.30.137:2181 \
-e dubbo.admin.root.password=root \
-e dubbo.admin.guest.password=guest \
chenchuxin/dubbo-admin 
```