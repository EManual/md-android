内容提供器用来存放和获取数据并使这些数据可以被所有的应用程序访问。它们是应用程序之间共享数据的唯一方法；不存在所有Android软件包都能访问的公共储存区域。
Android为常见数据类型（音频，视频，图像，个人联系人信息，等等）装载了很多内容提供器。你可以看到在android.provider包里列举了一些。你还能查询这些提供器包含了什么数据（尽管，对某些提供器，你必须获取合适的权限来读取数据）。
如果你想公开你自己的数据，你有两个选择：你可以创建你自己的内容提供器（一个ContentProvider子类）或者你可以给已有的提供器添加数据-如果存在一个控制同样类型数据的内容提供器且你拥有写的权限。
这篇文档是一篇关于如何使用内容提供器的简介。先是一个简短的基础知识讨论，然后探究如何查询一个内容提供器，如何修改内容提供器控制的数据，以及如何创建你自己的内容提供器。
#### 内容提供器的基础知识Content Provider Basics
内容提供器究竟如何在表层下保存它的数据依赖于它的设计者。但是所有的内容提供器实现了一个公共的接口来查询这个提供器和返回结果-增加，替换，和删除数据也是一样。
这是一个客户端直接使用的接口，一般是通过ContentResolver对象。你可以通过getContentResolver()从一个活动或其它应用程序组件的实现里获取一个ContentResolver：
```  
ContentResolver cr = getContentResolver();
```
然后你可以使用这个ContentResolver的方法来和你感兴趣的任何内容提供器交互。
当初始化一个查询时，Android系统识别查询目标的内容提供器并确保它正在运行。系统实例化所有的ContentProvider对象；你从来不需要自己做。事实上，你从来不会直接处理ContentProvider对象。通常，对于每个类型的ContentProvider只有一个简单的实例。但它能够和不同应用程序和进程中的多个ContentProvider对象通讯。进程间的交互通过ContentResolver和ContentProvider类处理。
#### 数据模型The data model
内容提供器以数据库模型上的一个简单表格形式暴露它们的数据，这里每一个行是一个记录，每一列是特别类型和含义的数据。比如，关于个人信息以及他们的电话号码可能会以下面的方式展示：
![img](P)  
每个记录包含一个数字的_ID字段用来唯一标识这个表格里的记录。IDs可以用来匹配相关表格中的记录-比如，用来在一张表格中查找个人电话号码并在另外一张表格中查找这个人的照片。
一个查询返回一个Cursor对象可在表格和列中移动来读取每个字段的内容。它有特定的方法来读取每个数据类型。所以，为了读取一个字段，你必须了解这个字段包含了什么数据类型。（后面会更多的讨论查询结果和游标Cursor对象）。
#### 唯一资源标识符URIs
每个内容提供器暴露一个公开的URI（以一个Uri对象包装）来唯一的标识它的数据集。一个控制多个数据集（多个表）的内容提供器为每一个数据集暴露一个单独的URI。所有提供器的URIs以字符串"content://"开始。这个content:形式表明了这个数据正被一个内容提供器控制着。
如果你正准备定义一个内容提供器，为了简化客户端代码和使将来的升级更清楚，最好也为它的URI定义一个常量。Android为这个平台所有的提供器定义了CONTENT_URI 常量。比如，匹配个人电话号码的表的URI和包含个人照片的表的URI是：（均由联系人Contacts内容提供器控制）
```  
android.provider.Contacts.Photos.CONTENT_URI 
```
类似的，最近电话呼叫的表和日程表条目的URI如下：
```  
android.provider.CallLog.Calls.CONTENT_URI 
android.provider.Calendar.CONTENT_URI 
```
这个URI常量被使用在和这个内容提供器所有的交互中。每个ContentResolver方法采用这个URI作为它的第一个参数。正是它标识了ContentResolver应该和哪个内容提供器对话以及这个内容提供器的哪张表格是其目标。
#### 查询一个内容提供器Querying a Content Provider
你需要三方面的信息来查询一个内容提供器：
用来标识内容提供器的URI
你想获取的数据字段的名字
这些字段的数据类型
如果你想查询某一条记录，你同样需要那条记录的ID。
#### 生成查询Making the query
你可以使用ContentResolver.query()方法或者Activity.managedQuery()方法来查询一个内容提供器。两种方法使用相同的参数序列，而且都返回一个Cursor对象。不过，managedQuery()使得活动需要管理这个游标的生命周期。一个被管理的游标处理所有的细节，比如当活动暂停时卸载自身，而活动重新启动时重新查询它自己。你可以让一个活动开始管理一个尚未被管理的游标对象，通过如下调用： Activity.startManagingCursor()。
无论query()还是managedQuery()，它们的第一个参数都是内容提供器的URI-CONTENT_URI常量用来标识某个特定的ContentProvider和数据集（参见前面的URIs）。
为了限制只对一个记录进行查询，你可以在URI后面扩展这个记录的_ID值-也就是，在URI路径部分的最后加上匹配这个ID的字符串。比如，如果ID是23，那么URI会是：
有一些辅助方法，特别是ContentUris.withAppendedId() 和Uri.withAppendedPath()，使得为URI扩展一个ID变得简单。所以，比如，如果你想在联系人数据库中查找记录23，你可能需要构造如下的查询语句：
```  
import android.provider.Contacts.People;
import android.content.ContentUris;
import android.net.Uri;
import android.database.Cursor;
// Use the ContentUris method to produce the base URI for the contact with _ID == 23.
Uri myPerson = ContentUris.withAppendedId(People.CONTENT_URI, 23);
// Alternatively, use the Uri method to produce the base URI.
// It takes a string rather than an integer.
Uri myPerson = Uri.withAppendedPath(People.CONTENT_URI, "23");
// Then query for this specific record:
Cursor cur = managedQuery(myPerson, null, null, null, null);
```
query() 和managedQuery()方法的其它参数限定了更多的查询细节。如下：
应该返回的数据列的名字。null值返回所有列。否则只有列出名字的列被返回。所有这个平台的内容提供器为它们的列定义了常量。比如，android.provider.Contacts.Phones类对前面说明过的通讯录中各个列的名字定义了常量ID, NUMBER, NUMBER_KEY, NAME, 等等。
指明返回行的过滤器，以一个SQL WHERE语句格式化。 null值返回所有行。（除非这个URI限定只查询一个单独的记录）。
#### 选择参数
返回行的排列顺序，以一个SQL ORDER BY语句格式化（不包含ORDER BY本身）。null值表示以该表格的默认顺序返回，有可能是无序的。
让我们看一个查询的例子吧，这个查询获取一个联系人名字和首选电话号码列表：
```  
import android.provider.Contacts.People;
import android.database.Cursor;
// Form an array specifying which columns to return. 
String[] projection = new String[] {
	People._ID,
	People._COUNT,
	People.NAME,
	People.NUMBER
};
// Get the base URI for the People table in the Contacts content provider.
Uri contacts = People.CONTENT_URI;
// Make the query. 
Cursor managedCursor = managedQuery(contacts,
projection, // Which columns to return 
null, // Which rows to return (all rows)
null, // Selection arguments (none)
// Put the results in ascending order by name
People.NAME + " ASC");
```
这个查询从联系人内容提供器中获取了数据。它得到名字，首选电话号码，以及每个联系人的唯一记录ID。同时它在每个记录的_COUNT字段告知返回的记录数目。
列名的常量被定义在不同的接口中-_ID和_COUNT 定义在BaseColumns里， NAME在PeopleColumns里，NUMBER在PhoneColumns里。Contacts.People类已经实现了这些接口，这就是为什么上面的代码实例只需要使用类名就可以引用它们的原因。
#### 查询的返回结果What a query returns
一个查询返回零个或更多数据库记录的集合。列名，默认顺序，以及它们的数据类型是特定于每个内容提供器的。但所有提供器都有一个_ID列，包含了每个记录的唯一ID。另外所有的提供器都可以通过返回_COUNT 列告知记录数目。它的数值对于所有的行而言都是一样的。
下面是前述查询的返回结果的一个例子：
![img](P)  
获取到的数据通过一个游标Cursor对象暴露出来，通过游标你可以在结果集中前后浏览。你只能用这个对象来读取数据。如果想增加，修改和删除数据，你必须使用一个ContentResolver对象。