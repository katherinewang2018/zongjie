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

### group\_concat\( \[distinct\] 要连接的字段 \[order by 排序字段 asc/desc  \] \[separator '分隔符'\] \)

https://blog.csdn.net/mary19920410/article/details/76545053

后面两个参数可以木有，就使用默认，默认的分隔符是逗号。

另外需要注意的是:

mysql的group\_concat默认连接长度为1024字符，也就是说你需要连接后的连接超过1024字符，它只会显示这么长，其余部分都会被截取丢掉。

可以通过修改配置文件，在mysql配置文件中添加如下这句，修改配置文件后记得需要重启mysql服务

```
group_concat_max_len = 102400
```



