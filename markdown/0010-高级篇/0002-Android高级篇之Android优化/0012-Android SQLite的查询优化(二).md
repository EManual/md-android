#### 四、子查询扁平化 
例子：
```  
SELECT a FROM (SELECT x+y AS a FROM t1 WHERE z<100) WHERE a>5
```
对这个SQL语句的执行一般默认的方法就是先执行内查询，把结果放到一个临时表中，再对这个表进行外部查询，这就要对数据处理两次，另外这个临时表没有索引，所以对外部查询就不能进行优化了，如果对上面的SQL进行处理后可以得到如下SQL语句：
```  
SELECT x+y AS a FROM t1 WHERE z<100 AND a>5
```
这个结果显然和上面的一样，但此时只需要对数据进行查询一次就够了，另外如果在表t1上有索引的话就避免了遍历整个表。
运用flatten方法优化SQL的条件：
1.子查询和外查询没有都用集函数
2.子查询没有用集函数或者外查询不是个表的连接
3.子查询不是一个左外连接的右操作数
4.子查询没有用DISTINCT或者外查询不是个表的连接
5.子查询没有用DISTINCT或者外查询没有用集函数
6.子查询没有用集函数或者外查询没有用关键字DISTINCT
7.子查询有一个FROM语句
8.子查询没有用LIMIT或者外查询不是表的连接
9.子查询没有用LIMIT或者外查询没有用集函数
10.子查询没有用集函数或者外查询没用LIMIT
11.子查询和外查询不是同时是ORDER BY子句
12.子查询和外查询没有都用LIMIT
13.子查询没有用OFFSET
14.外查询不是一个复合查询的一部分或者子查询没有同时用关键字ORDER BY和LIMIT
15.外查询没有用集函数子查询不包含ORDER BY
16.复合子查询的扁平化：子查询不是一个复合查询，或者他是一个UNION ALL复合查询，但他是都由若干个非集函数的查询构成，他的父查询不是一个复合查询的子查询，也没有用集函数或者是DISTINCT查询，并且在FROM语句中没有其它的表或者子查询，父查询和子查询可能会包含WHERE语句，这些都会受到上面11、12、13条件的限制。
```  
SELECT a+1 FROM (
	SELECT x FROM tab
	UNION ALL
	SELECT y FROM tab
	UNION ALL
	SELECT abs(z*2) FROM tab2
) WHERE a!=5 ORDER BY 1
```
转换为：
```  
SELECT x+1 FROM tab WHERE x+1!=5
UNION ALL
SELECT y+1 FROM tab WHERE y+1!=5
UNION ALL
SELECT abs(z*2)+1 FROM tab2 WHERE abs(z*2)+1!=5
ORDER BY 1
```
17.如果子查询是一个复合查询，那么父查询的所有的ORDER BY语句必须是对子查询的列的简单引用
18.子查询没有用LIMIT或者外查询不具有WHERE语句
子查询扁平化是由专门一个函数实现的，函数为：
```  
static int flattenSubquery(
	Parse *pParse, /* Parsing context */
	Select *p, /* The parent or outer SELECT statement */
	int iFrom, /* Index in p->pSrc->a[] of the inner subquery */
	int isAgg, /* True if outer SELECT uses aggregate functions */
	int subqueryIsAgg /* True if the subquery uses aggregate functions */
)
```
它是在Select.c文件中实现的。显然对于一个比较复杂的查询，如果满足上面的条件时对这个查询语句进行扁平化处理后就可以实现对查询的优化。如果正好存在索引的话效果会更好。