# Docker方式启动tomcat,访问首页出现404错误

## 场景:
在docker启动tomcat(版本是从阿里云上拉下的:8.5.50)时,访问tomcat首页时出现404错误,在网上找了许多教程,也没有解决,最后在视频讲解中查看到了问题(不知道是不是我拉下来版本的问题)

## 具体情况: 
使用命令: docker exec -it 运行的tomcat容器ID /bin/bash 进入到tomcat的目录
进入webapps文件夹,发现里面是空的(tomcat默认的欢迎页面实际上放在的路径应该是:webapps/ROOT/index.jsp或者index.html)
发现旁边还有个webapps.dist的文件,进入才发现原本应该在webapps文件中的文件都在webapps.dist文件中.



![](https://img-blog.csdnimg.cn/20200108221326635.png)

![](https://img-blog.csdnimg.cn/2020010822131581.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODkxMDA5,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200108221334990.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODkxMDA5,size_16,color_FFFFFF,t_70)

将webapps.dist重命名成webapps即可,原来的webapps(空文件)可以删除或者命名成其他的名字既:mv webapps.dist webapps

