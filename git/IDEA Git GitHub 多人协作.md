# IDEA Git GitHub 多人协作

> 使用GitHub作为远程仓库，使用Idea实现本地的版本控制，实现多人对同一个项目的协同开发，其中一人负责代码的合并和冲突处理。

## 代码分支

### master

- master 为主分支，也是用于部署生产环境的分支，确保master分支稳定性
- master 分支一般由dev以及hotfix分支合并，任何时间都不能直接修改代码

### dev

开发分支，代码审核之后合并到本分支，始终保持最新完成以及bug修复后的代码

### pro

生产环境分支，开发分支测试完成以后提交到此代码分支

### feature/*

功能分支:

- 开发新功能时，以dev为基础创建feature分支，开发完成以后合并到dev分支
- 分支命名: feature/ 开头的为特性分支， 命名规则: feature/user_module、 feature/cart_module

### hotfix/*

- 分支命名: hotfix/ 开头的为修复分支，它的命名规则与 feature 分支类似
- 线上出现紧急问题时，需要及时修复，以master分支为基线，创建hotfix分支，修复完成后，需要合并到master分支和develop分支







------

**下面的演示操作只是简单的对操作进行说明，所以master为开发分支，所有功能分支在开发完成之后即合并到master分支。**

------



## Idea创建本地仓库

按照原本的方法，创建一个Java项目：gittest，设置版本控制：

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200402233724.png)

## GitHub创建仓库

### 创建仓库

按照原始创建GitHub仓库的方式创建同名仓库，并创建Readme.md文件

## 本地仓库关联远程仓库

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200403000324.png)

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200403002503.png)

添加成功：

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200403002521.png)



推送分支进行测试：

```shell
Push failed
	ERROR: Permission to zcynemo/gittest.git denied to pomole.
	Could not read from remote repository.
	
	Please make sure you have the correct access rights
	and the repository exists.
```
这个时候我们发现虽然我们可以拉取远程仓库的代码，但是因为本地IDEA没有登录GitHub账户或者登录的账户不是对应远程仓库拥有者的账户，所以需要添加开发者：

### 关联协作开发GitHub账户

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200403001814.png)

点击添加以后，发送邮件到添加者账户：

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200403002033.png)

再次推送，推送成功！

## 开发新功能

创建新分支：

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200404005116.png)



![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200404005034.png)

创建完成，自动切换到新分支。

## 合并分支

开发完成新功能之后把此分支提交到远程仓库，

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200404010357.png)

pull所有commit，

可以在远程仓库看见新提交的分支：

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200404005506.png)



在执行合并的IDEA内切换分支为master

**一般的合并顺序：**

1. 拉取远端源时先提交本地代码
2. 本地提交记录与远端源合并，并解决冲突。
3. 解决冲突的时候，代表又修改了冲突的文件，我们需要重新提交
4. 上传解决的冲突到远端服务器

执行合并远程仓库的新功能分支

![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200404010832.png)



![](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200404010648.png)

如果代码没有冲突，即新功能合并完成。





## 解决合并冲突

IDEA中Git合并冲突

先commit本地修改的文件到本地repository

![这里写图片描述](https://img-blog.csdn.net/20180403085235594?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

pull源码，因为存在代码冲突，所以接下来会自动弹出merge融合窗口，如下图：

![这里写图片描述](https://img-blog.csdn.net/20180403085259239?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180402204310187?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![这里写图片描述](https://img-blog.csdn.net/20180402203800577?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

点击左边的 >> 试试

![这里写图片描述](https://img-blog.csdn.net/20180402203817150?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

点击右边的 << 试试

![这里写图片描述](https://img-blog.csdn.net/20180402203830908?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

最后结果

![这里写图片描述](https://img-blog.csdn.net/20180402203900609?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzgzNDM1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

