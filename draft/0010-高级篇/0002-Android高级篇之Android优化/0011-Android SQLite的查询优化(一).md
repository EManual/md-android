SQLite是个典型的嵌入式DBMS，它有很多优点，它是轻量级的，在编译之后很小，其中一个原因就是在查询优化方面比较简单，它只是运用索引机制来进行优化的，经过对SQLite的查询优化的分析以及对源代码的研究，我将SQLite的查询优总结如下：
#### 一、影响查询性能的因素
1．对表中行的检索数目，越小越好。
2．排序与否。
3．是否要对一个索引。
4．查询语句的形式。
#### 二、几个查询优化的转换
1．对于单个表的单个列而言，如果都有形如T.C=expr这样的子句，并且都是用OR操作符连接起来，形如： x = expr1 OR expr2 = x OR x = expr3 此时由于对于OR，在SQLite中不能利用索引来优化，所以可以将它转换成带有IN操作符的子句：x IN(expr1,expr2,expr3)这样就可以用索引进行优化，效果很明显，但是如果在都没有索引的情况下OR语句执行效率会稍优于IN语句的效率。
2．如果一个子句的操作符是BETWEEN，在SQLite中同样不能用索引进行优化，所以也要进行相应的等价转换： 如：a BETWEEN b AND c可以转换成：(a BETWEEN b AND c) AND (a>=b) AND (a<=c)。 在上面这个子句中， (a>=b) AND (a<=c)将被设为dynamic且是(a BETWEEN b AND c)的子句，那么如果BETWEEN语句已经编码，那么子句就忽略不计，如果存在可利用的index使得子句已经满足条件，那么父句则被忽略。
3．如果一个单元的操作符是LIKE，那么将做下面的转换：x LIKE ‘abc%’，转换成：x>=‘abc’ AND x<‘abd’。因为在SQLite中的LIKE是不能用索引进行优化的，所以如果存在索引的话，则转换后和不转换相差很远，因为对LIKE不起作用，但如果不存在索引，那么LIKE在效率方面也还是比不上转换后的效率的。
#### 三、 几种查询语句的处理（复合查询）
查询语句为：
```  
<SelectA> <operator> <selectB> ORDER BY <orderbylist> 
```
ORDER BY 执行方法： 
```  
is one of UNION ALL, UNION, EXCEPT, or INTERSECT. 
```
这个语句的执行过程是先将selectA和selectB执行并且排序，再对两个结果扫描处理，对上面四种操作是不同的，将执行过程分成七个子过程：
```  
outA: 将selectA的结果的一行放到最终结果集中
outB: 将selectA的结果的一行放到最终结果集中(只有UNION操作和UNION ALL操作，其它操作都不放入最终结果集中)
AltB: 当selectA的当前记录小于selectB的当前记录
AeqB: 当selectA的当前记录等于selectB的当前记录
AgtB: 当selectA的当前记录大于selectB的当前记录
EofA: 当selectA的结果遍历完
EofB: 当selectB的结果遍历完
```
下面就是四种操作的执行过程：
```  
UNION ALL
UNION
EXCEPT
INTERSECT
AltB:
outA, nextA
outA, nextA
outA,nextA
nextA
AeqB:
outA, nextA
nextA
nextA
outA, nextA
AgtB:
outB, nextB
outB, nextB
nextB
nextB
EofA:
outB, nextB
outB, nextB
halt
halt
EofB:
outA, nextA
outA, nextA
outA,nextA
halt
```
2．如果可能的话，可以把一个用到GROUP BY查询的语句转换成DISTINCT语句来查询，因为GROUP BY有时候可能会用到index，而对于DISTINCT都不会用到索引的。