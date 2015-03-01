在学习Android数据库SQLite之前，必须意识到这一点，目前在Android系统中集成的是SQLite3版本，SQLite是一个开源的嵌入式数据库，他支持NULL、INTEGER、REAL、TEXT和BLOB数据类型，不支持静态数据类型，而是使用列关系。可以把SQLite数据库近似看成是一种无数据类型的数据库，你可以把任何类型的资料存放在飞Integer类型的主键之外的其他字段上去，另外字段的长度也是没有限度的。不过建议一定要在编写SQL语句的时候，按照标准的SQL语法，因为这样在别人看你的代码时候，便于更好的理解。
SQLite可以解析大部分的标准SQL语句：   
```   
建表语句：create table 表名(主键名 integer primary key autoincrement，其他列名及属性)。
查询语句：select * from 表名 where 条件子句 group by 分组子句 having…order by 排序子句。 
分页语句：select * from 表名 limit 记录数 offset 开始位置 或者 select * from 表名 limit 开始位置，记录数。
插入语句：insert into 表名(字段列表) values (值列表)。
更新语句：update 表名 set 字段名=值 where 条件子句 删除语句：delete from 表名 where 条件子句。
删表语句：drop table if exists 表名。
```
而为了方便对数据库进行版本管理，建议在开发项目的时候使用SQLiteOpenHelper类，它提供了两个重要的方法，分别是onCreate(SQLiteDatabase db)和onUpgrade(SQLiteDatabase db，int oldVersion，int vewVersion)，前者用于初次使用软件时生成数据库，后者用于升级软件时更新数据库表结构。提示一下，在软件升级前，最好对原有数据进行备份，在新表建好后把数据导入新表中。实现了这两个方法，就可以用他的getWritableDatabase()和getReadableDatabase()来获得数据库。这里提醒一句，在使用SQLite的进行查询时最好用占位符“？”来代替各值，例如：
```  
SQLiteDatabase db=databaseHelper.getWritableDatabase();
db.execSQL(“update person set name=?,age=? where personid=?”,new Obect{person.getName(),person.getAge(),person.getId()});
```
execSQL()方法是用来执行除查找语句外的sql语句，查找语句用rawQuery()来执行它会放回一个Cursor。当然，他还提供了封装好的Java类方法供我们操作。具体方法不介绍了，可以直接查看文档中SQLiteDatabase类的用法。
那要查看数据库中的内容怎么办呢？一种方法是把数据库文件导出到电脑中，然后用SQLite Developer这个软件即可打开查看其中的结构和内容。另一种是直接用命令行查看(推荐)，可以通过adb shell进入模拟器的Linux控制台，找到数据库文件，用sqlite3数据库名的方式进入数据库，如此即可查看数据库中的内容。至于他的一些命令，也可以在cmd中输入.help来查看命令。
说道数据库，就不得不说一说事务。在Android中使用事务很容易，在执行sql语句之前调用SQLiteDatabase的beginTransaction()方法打开事务，执行完毕后调用endTransaction()方法来关闭事务即可。使用完数据库后一定要记得关闭数据库连接以防资源浪费。