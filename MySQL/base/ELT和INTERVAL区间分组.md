# ELT和INTERVAL

```sql
-- 数据准备
-- 用户表
CREATE TABLE `t_user` (
  `id` int(12) NOT NULL AUTO_INCREMENT,
  `name` varchar(64) DEFAULT NULL,
  `age` int(3) DEFAULT NULL,
  `mobile` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=10001 DEFAULT CHARSET=utf8;

DROP PROCEDURE
IF
	EXISTS proc_initData;--如果存在此存储过程则删掉 
DELIMITER $
CREATE PROCEDURE proc_initData () BEGIN
	DECLARE
		i INT DEFAULT 1;
	WHILE
			i <= 10000 DO
			INSERT INTO `test_2`.`t_user` ( `id`, `name`, `age`, `mobile` )
		VALUES
			( i, CONCAT( 'name', i ), FLOOR( 10 + ( RAND() * 60 )), FLOOR( 11000000000 + ( RAND() * 199999999999 )));
		SET i = i + 1;
	END WHILE;
END $ CALL proc_initData ();

-- 订单表
CREATE TABLE `t_order` (
  `id` int(12) NOT NULL AUTO_INCREMENT,
  `member_id` int(12) DEFAULT NULL,
  `amount` int(11) DEFAULT NULL COMMENT '数量',
  `money` int(11) DEFAULT NULL COMMENT '订单金额',
  `status` int(1) DEFAULT NULL COMMENT '订单状态（0：待付款；1：已付款；2：已退款；3：已退货；4：已取消）',
  `order_code` varchar(255) DEFAULT NULL COMMENT '订单编号',
  `order_time` datetime DEFAULT NULL COMMENT '下单时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=100001 DEFAULT CHARSET=utf8;

DROP PROCEDURE
IF
	EXISTS proc_initData;--如果存在此存储过程则删掉 
DELIMITER $
CREATE PROCEDURE proc_initData () BEGIN
	DECLARE
		i INT DEFAULT 1;
	WHILE
			i <= 100000 DO
			INSERT INTO `test_2`.`t_order` ( `id`, member_id, `amount`, `money`, `status`, `order_code`, `order_time` )
		VALUES
			(
				i,
				FLOOR(1 + ( RAND() * 10000 )),
				FLOOR(1 + ( RAND() * 10 )),
				FLOOR(10 + ( RAND() * 1000 )),
				FLOOR(0 + ( RAND() * 5 )),
				CONCAT('TE',FLOOR(11000000000 + ( RAND() * 199999999999 ))),
			DATE_ADD( '2021-01-01 00:00:00', INTERVAL FLOOR( 1 + ( RAND() * 1864000 )) SECOND )
	);
	SET i = i + 1;
END WHILE;
END $ CALL proc_initData ();


-- 会员身份
CREATE TABLE `t_member_type` (
  `id` int(12) NOT NULL AUTO_INCREMENT,
  `member_id` int(12) DEFAULT NULL,
  `member_type_id` int(2) DEFAULT NULL COMMENT '会员身份：1：vip;2:vvip',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO `test_2`.`t_member_type`(`id`, `member_id`, `member_type_id`) VALUES (1, 1, 1);

```



## 查询所有订单的金额分布

```sql
SELECT
	elt(
		INTERVAL ( money, 0, 10, 50, 100, 200, 500, 1000 ),
		'0~10',
		'10~50',
		'50~100',
		'100~200',
		'200~500',
		'500~1000',
		'>1000' 
	) AS 'money',
	count( id ) AS 'count' 
FROM
	t_order 
GROUP BY
	elt(
		INTERVAL ( money, 0, 10, 50, 100, 200, 500, 1000 ),
		'0~10',
		'10~50',
		'50~100',
		'100~200',
		'200~500',
		'500~1000',
	'>1000' 
	)
	
	
-- 结果
+----------+-------+
| money    | count |
+----------+-------+
| 100~200  |  9839 |
| 10~50    |  4023 |
| 200~500  | 30128 |
| 500~1000 | 50035 |
| 50~100   |  4995 |
| >1000    |   980 |
+----------+-------+
```

其中利用interval划出4个区间 ，再利用elt函数将4个区间分别返回一个列名；



## elt

```sql
-- ELT(N,str1,str2,str3,...)
-- 如果N= 1，返回str1，如果N= 2，返回str2，等等。如果N小于1或大于参数个数，返回NULL。ELT()是FIELD()反运算。

mysql> select elt(1,'1','2','3');
+--------------------+
| elt(1,'1','2','3') |
+--------------------+
| 1                  |
+--------------------+
1 row in set (0.05 sec)
```



## INTERVAL

```sql
-- ELT(N,str1,str2,str3,...)
-- N小于后面的某个参数，返回前一个参数表的位置，由前往后依次比较；
-- 中间位置
mysql> select interval(30,0,10,20,30,40,50);
+-------------------------------+
| interval(30,0,10,20,30,40,50) |
+-------------------------------+
|                             4 |
+-------------------------------+
1 row in set (0.06 sec)

-- 大于第一个位置
mysql> select interval(0,0,10,20,30,40,50);
+------------------------------+
| interval(0,0,10,20,30,40,50) |
+------------------------------+
|                            1 |
+------------------------------+
1 row in set (0.06 sec)

-- 等于最后一个位置
mysql> select interval(50,0,10,20,30,40,50);
+-------------------------------+
| interval(50,0,10,20,30,40,50) |
+-------------------------------+
|                             6 |
+-------------------------------+
1 row in set (0.05 sec)
-- 大于最后一个位置
mysql> select interval(100,0,10,20,30,40,50);
+--------------------------------+
| interval(100,0,10,20,30,40,50) |
+--------------------------------+
|                              6 |
+--------------------------------+
1 row in set (0.05 sec)
-- 小于第一个位置
mysql> select interval(-1,0,10,20,30,40,50);
+-------------------------------+
| interval(-1,0,10,20,30,40,50) |
+-------------------------------+
|                             0 |
+-------------------------------+
1 row in set (0.05 sec)
```



## 使用CASE WHEN 实现

```sql
mysql> SELECT
	sum( CASE WHEN money >= 0 AND money < 10 THEN 1 ELSE 0 END) AS '0~10',
	sum( CASE WHEN money >= 10 AND money < 50 THEN 1 ELSE 0 END) AS '10~50',
	sum( CASE WHEN money >= 50 AND money < 100 THEN 1 ELSE 0 END) AS '50~100',
	sum( CASE WHEN money >= 100 AND money < 200 THEN 1 ELSE 0 END) AS '100~200',
	sum( CASE WHEN money >= 200 AND money < 500 THEN 1 ELSE 0 END) AS '200~500',
	sum( CASE WHEN money >= 500 AND money < 1000 THEN 1 ELSE 0 END) AS '500~1000',
	sum( CASE WHEN money >= 1000 THEN 1 ELSE 0 END) AS '>1000'
	from t_order;
+------+-------+--------+---------+---------+----------+-------+
| 0~10 | 10~50 | 50~100 | 100~200 | 200~500 | 500~1000 | >1000 |
+------+-------+--------+---------+---------+----------+-------+
| 0    | 4023  | 4995   | 9839    | 30128   | 50035    | 980   |
+------+-------+--------+---------+---------+----------+-------+
1 row in set (0.17 sec)
```

