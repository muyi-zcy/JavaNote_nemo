# GitHub如何配置SSH Key

# 遇到的问题
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329150706786.png)
当我们第一次把本地项目推送到远程仓库的时候，会出现这样的情况。

# 查看本地配置
```shell
git config --list
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329151146461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MjA2NjE2,size_16,color_FFFFFF,t_70)
如果没有配置，配置本地账户：
```shell
git config --global user.name "nemo"
git config --global user.email  "zcy_nemo@aliyun.com"
```
# 检查本地是否存在ssh key 
```shell
cd ~/.ssh

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/202003291513567.png)
如果没有，执行：
```shell
 ssh-keygen -t rsa -C "zcy_nemo@aliyun.com"
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329151500114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MjA2NjE2,size_16,color_FFFFFF,t_70)

检查结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329151523789.png)

查看SSH Key：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329151548270.png)

# 添加GitHub SSH keys
打开github ->setting
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329151708646.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329151653518.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MjA2NjE2,size_16,color_FFFFFF,t_70)
新建ssh key,输入本地的ssk key内容，随便取个名字，提交之后输入密码即可。

# 验证结果
```shell
ssh -T git@github.com
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329151907355.png)