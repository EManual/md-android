在SQL Server 2005中运行如下T-SQL语句，假定SALES表中有多行数据，执行查询之后的结果是（）。
```  
BEGIN TRANSACTION A
	Update SALES Set qty=30 WHERE qty<30
	BEGIN TRANSACTION B
		Update SALES Set qty=40 WHERE qty<40
		Update SALES Set qty=50 WHERE qty<50
		Update SALES Set qty=60 WHERE qty<60
	COMMIT　TRANSACTION B
COMMIT TRANSACTION A
A、SALES表中qty列最小值大于等于30
B、SALES表中qty列最小值大于等于40
C、SALES表中qty列的数据全部为50
D、SALES表中qty列最小值大于等于60
```
#### 答案：D