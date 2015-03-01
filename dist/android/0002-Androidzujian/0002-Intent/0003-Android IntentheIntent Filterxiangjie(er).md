#### Data
需要操作的数据的URI和它的MIME(多用途互联网邮件扩展，Multipurpose Internet Mail Extensions)类型。 例如，如果action为ACTION_EDIT，那么Data将包含待编辑的数据URI。如果action为ACTION_CALL，Data将为tel:电话号码的URI。如果action为ACTION_VIEW，则Data为http:网络地址的URI。
当将一个intent和一个组件相匹配时，除了URI外数据类型也很重要. 例如，一个显示图片的程序不应该用来处理声音文件。
数据类型常常可以从URI推断，特别是content:URI，它表示该数据属于一个content provider。但数据类型也可以被intent对象显示声明。setData()方法设置URI，而setType()方法指定MIME类型，setDataAndType()设置数据URI和MIME类型。它们可以使用getData()和getType()来读取。
#### Category
一个字符串，包含了关于处理该intent的组件的种类的信息。一个intent对象可以有任意个category.。intent类定义了许多category常数，例如:
```  
常量	含义
CATEGORY_BROWSABLE	目标activity可以使用浏览器来显示-例如图片或电子邮件消息。
CATEGORY_GADGET	该activity可以被包含在另外一个装载小工具的activity中。
CATEGORY_HOME	该activity显示主屏幕，也就是用户按下Home键看到的界面。
CATEGORY_LAUNCHER	该activity可以作为一个任务的第一个activity，并且列在应用程序启动器中。
CATEGORY_PREFERENCE	该activity是一个选项面板。
```
addCategory()方法为一个intent对象增加一个category，removeCategory删除一个category，getCategories()获取intent所有的category。
#### Extras
为键值对形式的附加信息。 例如ACTION_TIMEZONE_CHANGED的intent有一个"time-zone"附加信息来指明新的时区，而ACTION_HEADSET_PLUG有一个"state"附加信息来指示耳机是被插入还是被拔出。
intent对象有一系列put...()和set...()方法来设定和获取附加信息. 这些方法和Bundle对象很像。 事实上附加信息可以使用putExtras()和getExtras()作为Bundle来读和写。
#### Flags
各种标志。很多标志指示android系统如何启动一个activity(例如该activity属于哪个任务)和启动后如何处理它(例如, 它是否属于最近activity列表中)。
android系统和应用程序使用intent对象来送出系统广播和激活系统定义的组件.
#### Intent Resolution Intent解析
intent有两种:
1、显式intent使用名字来指定目标组件。由于组件名称一般不会被其它开发者所熟知，这种intent一般用于应用程序内部消息-- 例如一个activity启动一个附属的service或者另一个activity。
2、隐式intent不指定目标的名称. 一般用于启动其它应用程序的组件。
Android将显式intent发送给指定的类。intent对象中名字唯一决定接受intent的对象.对于隐式intent，android系统必须找到最合适的组件来处理它。它比较intent的内容和intent filter。intent filter是组件的一个相关结构，表示其接受intent的能力，android系统根据intent filter打开可以接受intent的组件。如果一个组件没有intent filter，那么它只能接受显式intent。如果有，则能同时接受二者。当一个intent和intent filter比较时，只考虑三个属性: action，data，category.extra和flag在intent解析中没有用。
#### Intent filters
activity, service和broadcast receiver可以有多个intent filter来告知系统它们能接受什么样的隐式intent。intent filter的名字很形象: 它过滤掉不想接受的intent，留下想接受的intent。显式intent无视intent filter。一个组件对能做的每件事有单独的filter。例如，记事本程序的NoteEditor activity有两个filter -- 一个启动并显示一个特定的记录给用户查看或编辑，另一个启动一个空的记录给用户编辑。
#### Filters and security Filter和安全
一个intent filter不一定安全可靠。一个应用程序可以让它的某个组件去接受隐式intent，但是它没法防止这个组件接受显示intent。 其它的程序总是可以使用自定义的数据加上显式的程序名称来调用该组件。
一个intent filter是IntentFilter类的实例，但是它一般不出现在代码中，而是出现在android Manifest文件中，以<intent-filter>的形式。(有一个例外是broadcast receiver的intent filter是使用Context.registerReceiver()来动态设定的，其intent filter也是在代码中创建的。)
一个filter有action，data，category等字段。一个隐式intent为了能被某个intent filter接受，必须通过3个测试。一个intent为了被某个组件接受，则必须通过它所有的intent filter中的一个。
#### Action 测试
```  
<intent-filter >
	<action android:name="com.example.project.SHOW_CURRENT" />
	<action android:name="com.example.project.SHOW_RECENT" />
	<action android:name="com.example.project.SHOW_PENDING" />
	. . .
</intent-filter>
```