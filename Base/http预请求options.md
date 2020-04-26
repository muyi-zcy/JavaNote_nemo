# http预请求options

在有很多情况下,当我们在js里面调用一次ajax请求时,在浏览器那边却会查询到两次请求,第一次的Request Method参数是OPTIONS,还有一次就是我们真正的请求,比如get或是post请求方式

大致说明一下,有三种方式会导致这种现象:

1:请求的方法不是GET/HEAD/POST

2:POST请求的Content-Type并非application/x-www-form-urlencoded, multipart/form-data, 或text/plain

3:请求设置了自定义的header字段

比如我的我的Content-Type设置为“application/json;charset=utf-8”并且自定义了header选项导致了这种情况。