这个类主要是Android用来实现应用程序之间数据共享的。
一个应用程序可以将自己的数据完全暴露出去，外界更本看不到，也不用看到这个应用程序暴露的数据是如何存储的，或者是使用数据库还是使用文件，还是通过网上获得，这些一切都不重要，重要的是外界可以通过这一套标准及统一的接口和这个程序里的数据打交道，例如：添加(insert)、删除(delete)、查询(query)、修改(update)，当然需要一定的权限才可以。
一个程序可以通过实现一个Content provider的抽象接口将自己的数据完全暴露出去，而且Content providers是以类似数据库中表的方式将数据暴露。Content providers存储和检索数据，通过它可以让所有的应用程序访问到，这也是应用程序之间唯一共享数据的方法。
要想使应用程序的数据公开化，可通过2种方法：
1）创建一个属于你自己的Content provider。
2）将你的数据添加到一个已经存在的Content provider中，前提是有相同数据类型并且有写入Content provider的权限。
什么是URI？在学习如何获取ContentResolver前，有个名词是必须了解的：URI。URI是网络资源的定义，在Android中赋予其更广阔的含义，先看个例子，如下：
将其分为A，B，C，D 4个部分：
A：标准前缀，用来说明一个Content Provider控制这些数据，无法改变的；
B：URI的标识，它定义了是哪个Content Provider提供这些数据。对于第三方应用程序，为了保证URI标识的唯一性，它必须是一个完整的、小写的   类名。这个标识在<provider> 元素的 authorities属性中说明：
<provider name=”.TransportationProvider”  authorities=”com.example.transportationprovider”  . . .  >
C：路径，Content Provider使用这些路径来确定当前需要生什么类型的数据，URI中可能不包括路径，也可能包括多个；
D：如果URI中包含，表示需要获取的记录的ID；如果没有ID，就表示返回全部；
由于URI通常比较长，而且有时候容易出错，切难以理解。所以，在Android当中定义了一些辅助类，并且定义了一些常量来代替这些长字符串，例如：People.CONTENT_URI
ContentResolver介绍说明看完这些介绍，大家一定就明白了，ContentResolver是通过URI来查询ContentProvider中提供的数据。除了URI以外，还必须知道需要获取的数据段的名称，以及此数据段的数据类型。如果你需要获取一个特定的记录，你就必须知道当前记录的ID，也就是URI中D部分。
前面也提到了Content providers是以类似数据库中表的方式将数据暴露出去，那么ContentResolver也将采用类似数据库的操作来从Content providers中获取数据。现在简要介绍ContentResolver的主要接口，如下：
```  
返回值	函数声明
final Uri	insert (Uri url, ContentValues values)Inserts a row into a table at the given URL.
final int	delete (Uri url, String where, String[] selectionArgs)Deletes row(s) specified by a content URI.
final Cursor	query (Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)Query the given URI, returning a Cursor over the result set.
final int	update (Uri uri, ContentValues values, String where, String[] selectionArgs)Update row(s) in a content URI.
```
可能还有2个问题，是需要关注的。
1.ContentProvider是什么时候创建的，是谁创建的？访问某个应用程序共享的数据，是否需要启动这个应用程序？这个问题在 Android SDK中没有明确说明，但是从数据共享的角度出发，ContentProvider应该是Android在系统启动时就创建了，否则就谈不上数据共享了。这就要求在AndroidManifest.XML中使用<provider>元素明确定义。
2.可能会有多个程序同时通过ContentResolver访问一个ContentProvider，会不会导致像数据库那样的“脏数据”？这个问题一方面需要数据库访问的同步，尤其是数据写入的同步，在AndroidManifest.XML中定义ContentProvider的时候，需要考虑是<provider>元素multiprocess属性的值；另外一方面Android在ContentResolver中提供了notifyChange()接口，在数据改变时会通知其他ContentObserver，这个地方应该使用了观察者模式，在ContentResolver中应该有一些类似register，unregister的接口。