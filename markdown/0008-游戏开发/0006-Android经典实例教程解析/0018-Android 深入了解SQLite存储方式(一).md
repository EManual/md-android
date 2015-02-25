#### 什么是SQLite：
SQLite是一款轻量级数据库，它的设计目的是嵌入式，而且它占用的资源非常少，在嵌入式设备中，只需要几百KB!
SQLite的特性：
#### 轻量级
使用 SQLite 只需要带一个动态库，就可以享受它的全部功能，而且那个动态库的尺寸想当小。
#### 独立性
SQLite 数据库的核心引擎不需要依赖第三方软件，也不需要所谓的“安装”。
#### 隔离性
SQLite 数据库中所有的信息（比如表、视图、触发器等）都包含在一个文件夹内，方便管理和维护。
#### 跨平台
SQLite 目前支持大部分操作系统，不至电脑操作系统更在众多的手机系统也是能够运行，比如：Android。
#### 多语言接口
SQLite 数据库支持多语言编程接口。
#### 安全性
SQLite数据库通过数据库级上的独占性和共享锁来实现独立事务处理。这意味着多个进程可以在同一时间从同一数据库读取数据，但只能有一个可以写入数据。
#### 优点：
1.能存储较多的数据。
2.能将数据库文件存放到SD卡中！
#### 什么是 SQLiteDatabase？ 
一个SQLiteDatabase的实例代表了一个SQLite的数据库，通过SQLiteDatabase实例的一些方法，我们可以执行SQL语句，对数据库进行增、删、查、改的操作。需要注意的是，数据库对于一个应用来说是私有的，并且在一个应用当中，数据库的名字也是惟一的。
#### 什么是SQLiteOpenHelper？
根据这名字，我们可以看出这个类是一个辅助类。这个类主要生成一个数据库，并对数据库的版本进行管理。当在程序当中调用这个类的方法getWritableDatabase()，或者getReadableDatabase()方法的时候，如果当时没有数据，那么Android系统就会自动生成一个数据库。SQLiteOpenHelper 是一个抽象类，我们通常需要继承它，并且实现里边的3个函数。
#### 什么是ContentValues类？
ContentValues类和Hashmap/Hashtable比较类似，它也是负责存储一些名值对，但是它存储的名值对当中的名是一个String类型，而值都是基本类型。
#### 什么是Cursor？
Cursor在Android 当中是一个非常有用的接口，通过Cursor 我们可以对从数据库查询出来的结果集进行随机的读写访问。
OK，基本知识就介绍到这里，下面开始上代码：还是按照我的一贯风格，代码中该解释的地方都已经在代码中及时注释和讲解了！
顺便来张项目截图：
![img](P)  
我们先来看看xml的代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/tv_title"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="SQL 练习！（如果你使用的SD卡存储数据方式，为了保证正常操作，请你先点击创建一张表然后再操作）"
        android:textColor="ff0000"
        android:textSize="20sp" />
    <Button
        android:id="@+id/sql_addOne"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="插入一条记录" >
    </Button>
    <Button
        android:id="@+id/sql_check"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="查询数据库" >
    </Button>
    <Button
        android:id="@+id/sql_edit"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="修改一条记录" >
    </Button>
    <Button
        android:id="@+id/sql_deleteOne"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="删除一条记录" >
    </Button>
    <Button
        android:id="@+id/sql_deleteTable"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="删除数据表单" >
    </Button>
    <Button
        android:id="@+id/sql_newTable"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="新建数据表单" >
    </Button>
</LinearLayout>
```