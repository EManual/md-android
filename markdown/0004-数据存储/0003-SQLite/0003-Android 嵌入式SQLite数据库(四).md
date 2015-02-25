#### 事务管理
1.使用在SQLite数据库时可以使用SQLiteDatabase类中定义的相关方法控制事务
```  
beginTransaction() 开启事务
setTransactionSuccessful() 设置事务成功标记
endTransaction() 结束事务
```
2.endTransaction()需要放在finally中执行，否则事务只有到超时的时候才自动结束，会降低数据库并发效率
```  
public void remit(int from, int to, int amount) {
	SQLiteDatabase db = helper.getWritableDatabase();
	// 开启事务
	try {
		db.beginTransaction();
		db.execSQL("UPDATE person SET balancebalance=balance-? WHERE id=?",
				new Object[] { amount, from });
		System.out.println(1 / 0);
		db.execSQL("UPDATE person SET balancebalance=balance+? WHERE id=?",
				new Object[] { amount, to });
		// 设置事务标记
		db.setTransactionSuccessful();
	} finally {
		// 结束事务
		db.endTransaction();
	}
	db.close();
}
```