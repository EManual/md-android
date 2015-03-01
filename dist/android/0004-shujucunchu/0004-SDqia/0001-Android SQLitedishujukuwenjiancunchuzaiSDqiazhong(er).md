一种存储默认系统路径/data-data-com.himi-databases下,另外一种则是保存在了/sdcard-ac下，生成数据库文件demo.db
那么这里两种实现方式大概步骤和区别说下：
如果我们使用默认系统路径存储数据库文件：
第一步：新建一个类继承SQLiteOpenHelper;写一个构造，重写两个函数！
第二步：在新建的类中的onCreate(SQLiteDatabase db) 方法中创建一个表；
第三步：在进行删除数据、添加数据等操作的之前我们要得到数据库读写句柄得到一个数据库实例；
#### 注意： 
继承写这个辅助类，是为了在我们没有数据库的时候自动为我们生成一个数据库，并且生成数据库文件，这里也同时创建了一张表，因为我们在onCreate里是在数据库中创建一张表的操作；这里还要注意在我们new这个我们这个MySQLiteOpenHelper 类实例对象的时候并没有创建数据库哟~！而是在我们调用 MySQLiteOpenHelper ..getWritableDatabase() 这个方法得到数据库读写句柄的时候，android会分析是否已经有了数据库，如果没有会默认为我们创建一个数据库并且在系统路径data-data-com.himi-databases下生成ac.db文件!
(如果我们使用sd卡存储数据库文件，就没有必要写这个类了，而是我们自己Open自己的文件得到一个数据库,西西,反而方便~ )
如果我们需要把数据库文件存储到SD卡中：
第一步：确认模拟器存在SD卡，关于SD卡的两种创建方法
第二步：（备注1）先创建SD卡目录和路径已经我们的数据库文件！这里不像上面默认路径中的那样，如果没有数据库会默认系统路径生成一个数据库和一个数据库文件！我们必须手动创建数据库文件！
第三步：在进行删除数据、添加数据等操作的之前我们要得到数据库读写句柄得到一个数据库实例；(备注2)此时的创建也不是像系统默认创建，而是我们通过打开第一步创建好的文件得到数据库实例。这里仅仅是创建一个数据库！！！！
第四步：在进行删除数据、添加数据等操作的之前我们还要创建一个表！
第五步：在配置文件AndroidMainfest.xml 声明写入SD卡的权限，上一篇已经介绍权限了，不知道的自己去看下吧。
有些童鞋不理解什么默认路径方式中就有表？那是因为我们在它默认给我们创建数据库的时候我们有创建表的操作，就是MySQLiteOpenHelper类中的onCreate()方法里的操作！所以我们如果要在进行删除数据、添加数据等操作的之前还要创建一个表，创建表的方法都是一样的。
总结：不管哪种方式我们都要-创建数据库-创建表-然后进行操作！
在Android中查询数据是通过Cursor类来实现的，当我们使用SQLiteDatabase.query()方法时，会得到一个Cursor对象，Cursor指向的就是每一条数据。它提供了很多有关查询的方法，具体方法如下：
以下是方法和说明：
```  
move  以当前的位置为参考，将Cursor移动到指定的位置，成功返回true, 失败返回false。
moveToPosition  将Cursor移动到指定的位置，成功返回true,失败返回false。
moveToNext将Cursor向前移动一个位置，成功返回true,失败返回false。
moveToLast  将Cursor向后移动一个位置，成功返回true,失败返回 false。
movetoFirst将Cursor移动到第一行，成功返回true,失败返回false。
isBeforeFirst  返回Cursor是否指向第一项数据之前。
isAfterLast返回Cursor是否指向最后一项数据之后。
isClosed返回Cursor是否关闭。
isFirst  返回Cursor是否指向第一项数据。
isLast返回Cursor是否指向最后一项数据。
isNull返回指定位置的值是否为null。
getCount 返回总的数据项数。
getInt  返回当前行中指定的索引数据。
```。