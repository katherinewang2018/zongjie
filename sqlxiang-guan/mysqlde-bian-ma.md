SUBSTRING  \( expression, start, length \)

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

（1）如果 start的索引是从1开始，则从表达式的第一个字符开始进行字符串截取，从2开始就从表达式的第二个字符开始截取，以此类推。

例如:

 select SUBSTRING\('abcde',1,2\) 返回结果 ab

 select SUBSTRING\('abcde',2,3\) 返回结果 bcd

 select SUBSTRING\('abcde',1,0\) 返回结果为空

 select SUBSTRING\('abcde',0,8\) 返回结果为abcde,注意后面没有空格了。

\(2\) 如果start的索引是从小于1（**0或负数**）开始，则返回长度等于从1开始,截取长度为 length - \(\(start - 1\)的绝对值\)， 如果这个差为负数就返回空。

画个图理解这种情况（比如SUBSTRING\('abcde',0,2\)返回a）：

坐标：0　　 　　　　1 　　　　　　　　2 　　　　3　　　　 4　　　　5  
内容：空　　　　　　a            　　　　b 　　      c 　　　　d　　　   e  
截取：从此截取        截取到这里结束







