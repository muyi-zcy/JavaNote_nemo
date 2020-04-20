# MySQL表锁了如何解锁

1、查进程，查找被锁表的那个进程的ID

show processlist;

2、kill掉锁表的进程ID

kill id;

3、查询是否锁表

show OPEN TABLES where In_use > 0;

