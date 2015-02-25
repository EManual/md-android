1.先将已有的.db文件拷贝到android默认的目录下。
很多人就在这里挡住了，怎么copy呢？这里就要用到android自带的tools工具了。
首先，查询默认目录有哪些.db
[开始 - cmd  -输入adb shell --回车（也可开始 - adb shell）] 这样启动了adb.exe窗口
ls:显示目录
cd 目录名 :进入目录，有人问那返回上一目录是什么？回答cd ..（注意cd后有一空隔）    
通过连续的ls，cd就能看到我们默认db目录是在/data/data/[你的包名]如com.android/databases/    
好，知道目录径了，下面把我们的.db拷贝到下面。
[开始 - cmd ]进入普通cmd窗口。
输入:adb push D:/feiv.db /data/data/com.android/databases/feiv.db [将本地文件拷贝到默认android目录]
adb pull /data/data/com.android/databases/feiv.db D:/feiv.db  [当然这个是拷出来啦]
一点注意：是[/data]而不是[data]
经过上面的操作后，你再在adb.exe窗口里输入ls就能看到刚拷进去的文件了。
2.好了，有.db了，现在就是在代码里用就可以了。
用法很多地方也有介绍
我这里用this.sqliteDb = mcontext.openOrCreateDatabase(DATABASE_NAME, Context.MODE_PRIVATE, null);
openOrCreateDatabase()这个方法是打开一个db，如果没有的话，则会创建。
对了，还值得提一点，就是android里用simpleCursorAdapter要求表里字段必须有"_id"这样的字段，不知道Android平台为什么这样设计？不然查询报错。而我们导入进去的表里可能没有含有_id这样的字段，所以必须修改或增加。