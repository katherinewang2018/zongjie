### SUBSTRING  \( expression, start, length \)

参数

**expression**

字符串、二进制字符串、文本、图像、列或包含列的表达式。请勿使用包含聚合函数的表达式。

**start**

整数或可以隐式转换为 int 的表达式，指定子字符串的开始位置，索引是从1开始。

**length**

整数或可以隐式转换为 int 的表达式，指定子字符串的长度。经测试，暂且发现**只能是非负数 **

#### **返回值**

1.如果 expression 是一种支持的二进制数据类型，则返回二进制数据，这种情况我们暂且不讨论。  
2.如果 expression 是一种支持的字符数据类型，则返回字符数据。

（1）**如果 start的索引是从1开始**，则从表达式的第一个字符开始进行字符串截取，从2开始就从表达式的第二个字符开始截取，以此类推。

例如:

select SUBSTRING\('abcde',1,2\) 返回结果 ab

select SUBSTRING\('abcde',2,3\) 返回结果 bcd

select SUBSTRING\('abcde',1,0\) 返回结果为空

select SUBSTRING\('abcde',0,8\) 返回结果为abcde,注意后面没有空格了。

\(2\) 如果start的索引是从小于1（**0或负数**）开始，则返回长度等于从1开始,截取长度为 length - \(\(start - 1\)的绝对值\)， 如果这个差为负数就返回空。

画个图理解这种情况（比如SUBSTRING\('abcde',0,2\)返回a）：

坐标：0　　 　　　　1 　　　　　　　　2 　　　　3　　　　 4　　　　5  
内容：空　　　　　　a            　　　　b 　　      c 　　　　d　　　   e  
截取：从此截取        截取到这里结束

例如：下面的 \|\| 表示绝对值计算

select SUBSTRING\('abcde',0,2\) 返回结果为 a , 计算公式为SUBSTRING\(1,2-\|2-1\|\)

select SUBSTRING\('abcde',0,-1\) 返回错误提示“传递到 substring 函数的长度参数无效”

select SUBSTRING\('abcde',-1,2\) 返回结果为空, 计算公式为SUBSTRING\(1,2-\|-1-1\|\)

select SUBSTRING\('abcde',-5,3\) 返回结果为空, 计算公式为SUBSTRING\(1,3-\|-5-1\|\)

select SUBSTRING\('abcde',-1,4\) 返回结果为ab, 计算公式为SUBSTRING\(1,4-\|-1-1\|\)

select SUBSTRING\('abcde',-2,8\) 返回结果为abcde, 计算公式为SUBSTRING\(1,8-\|-2-1\|\)

### group\_concat\( \[distinct\] 要连接的字段 \[order by 排序字段 asc/desc  \] \[separator '分隔符'\] \)

[https://blog.csdn.net/mary19920410/article/details/76545053](https://blog.csdn.net/mary19920410/article/details/76545053)

[https://blog.csdn.net/u011280342/article/details/78285608](https://blog.csdn.net/u011280342/article/details/78285608)

后面两个参数可以木有，就使用默认，默认的分隔符是逗号。

另外需要注意的是:

mysql的group\_concat默认连接长度为1024字符，也就是说你需要连接后的连接超过1024字符，它只会显示这么长，其余部分都会被截取丢掉。

可以通过修改配置文件，在mysql配置文件中添加如下这句，修改配置文件后记得需要重启mysql服务

```
group_concat_max_len = 102400
```

### CAST函数语法规则是：Cast\(字段名 as 转换的类型 \)，其中类型可以为：

CHAR\[\(N\)\] 字符型

DATE 日期型

DATETIME 日期和时间型

DECIMAL float型

SIGNED int

TIME 时间型

[https://www.cnblogs.com/yangchunze/p/6667502.html](https://www.cnblogs.com/yangchunze/p/6667502.html)

### [数据类型DECIMAL用法](https://www.cnblogs.com/owenma/p/7097602.html)

### [https://blog.csdn.net/u010002184/article/details/79238624](https://blog.csdn.net/u010002184/article/details/79238624)

### [https://www.cnblogs.com/owenma/p/7097602.html](https://www.cnblogs.com/owenma/p/7097602.html)

定义某个字段是decimal类型，使用：

```
column_name  DECIMAL(P,D);
```

在上面的语法中：

```
P是表示有效数字数的精度。 P范围为1〜65。
D是表示小数点后的位数。 D的范围是0~30。MySQL要求D小于或等于(<=)P。
```

与INT数据类型一样，`DECIMAL`类型也具有`UNSIGNED`和`ZEROFILL`属性。 如果使用`UNSIGNED`属性，则`DECIMAL UNSIGNED`的列将不接受负值。

### mysql中的内置函数

```
SELECT CURRENT_TIME(); 20:30:20
SELECT NOW(); 2018-02-11 20:22:22
SELECT SYSDATE(); 2018-02-11 20:22:22
SELECT CURRENT_DATE() FROM DUAL; 2018-02-11
```

```
SELECT * FROM coin_data WHERE DATE_SUB(CURDATE(), INTERVAL 7 DAY) <= DATE(past_time);//获取七天内的数据
```

```
//查询某个时间段是否在某个小时内
SELECT * FROM dapp_access_hour
 WHERE dapp_access_day_id = #para(dappAccessDayId) AND #para(countHour) >=  count_hour
 AND #para(countHour) < DATE_ADD(count_hour,INTERVAL '1' HOUR)
#end
```

另一种\(做列表查询时 尽量注意排序问题\)：

```
SELECT * FROM dapp_access_hour
 WHERE dapp_access_day_id = ?
 AND HOUR(count_hour) = HOUR(?) //hour函数可以获得时间中的小时
 ORDER BY dapp_access_hour_id ASC
```

### mysql的时间函数

```
SELECT hour('12:13:14');                  # 12
SELECT hour('122:13:14');                 # 122
SELECT hour('12-13-14');                  # 0
SELECT hour('2008-09-10 12:13:14');       # 12
SELECT hour('2008-09-10 122:13:14');      # null
SELECT hour('2008-09-10 12-13-14');       # 12

SELECT minute('12:13:14');                # 13
SELECT minute('12:60:14');                # null
SELECT minute('12-13-14');                # 0
SELECT minute('2008-09-10 12:13:14');     # 13
SELECT minute('2008-09-10 122:13:14');    # null
SELECT minute('2008-09-10 12-13-14');     # 13

SELECT second('12:13:14');                # 14
SELECT second('12:13:60');                # null
SELECT second('12-13-14');                # 12
SELECT second('2008-09-10 12:13:14');     # 14
SELECT second('2008-09-10 122:13:14');    # null
SELECT second('2008-09-10 12-13-14');     # 14
```

```
SELECT LAST_DAY('2009-02-18 15:45:53');
SELECT TIME_FORMAT("97:15:40","%H %k %h %I %l");
SELECT TIMEDIFF("2009-05-18 15:45:57.005678","2009-05-18 13:40:50.005670");
SELECT TIMESTAMPADD(MONTH,2,'2009-05-18');
```

```
SELECT left('chinese', 4);        # chin
SELECT left(NULL , 4);            # null
SELECT left('chinese', NULL);     # null
SELECT right('chinese', 3);       # ese
SELECT right(NULL , 3);           # null
SELECT right('chinese', NULL );   # null
```

### LOWER\(str\)函数

返回字符串str，改为根据当前字符集映射为小写字母。

### sql的优化

[https://www.cnblogs.com/yunfeifei/p/3850440.html](https://www.cnblogs.com/yunfeifei/p/3850440.html)

[https://www.cnblogs.com/exe19/p/5786806.html](https://www.cnblogs.com/exe19/p/5786806.html)

避免引擎全表扫描的操作符（!= 或 &lt;&gt;）

避免使用or in 和 not in

    SELECT `column` FROM `table` WHERE `field` like 'keyword%';//SQL语句使用了索引

    SELECT `column` FROM `table` WHERE `field` like '%keyword%'; //SQL语句没用使用索引,避免使用这种查询

SHOW INDEX FROM 表名;  //可以查询表的index\_type 

BTREE

