这次主要要做的就是根据姓名来查找电话，并且加强对通讯录的理解。
以前做一些用到数据库的东西的时候，可能光看代码也是不好联系起各个数据之间的关系，所以我先想到的还是数据库。幸运的是，它还真是一个数据库。
Android里面内置的是SQLite的数据库，虽然对数据库不怎么了解，但关系型数据库，基本操作也就那些，而且基本都一样，所以就直接用呗。
用命令行下的adb shell进入Android的模拟器，进入data/data目录下面，这里面就是安装的一些应用程序。找啊找，里面有一个com.android.providers.contacts，怎么看都是一个通讯录相关的程序，进入这个目录下，里面有一个databases，就它了，再进去就可以看到有个contacts2.db的文件。
用sqlite3打开这个数据库文件。查看里面的表。里面表很多，不过看两遍后发现表的名字很熟悉，像什么data,raw_contacts,contacts,minetypes等，前面几个都是我们上次说的那几个所谓的数据模型，它们还真是数据库。

![img](http://emanual.github.io/md-android/img/device_contact/02_contact.jpg) 

查询一下data表里面的所有信息，可以发现里面的信息联系起来就都是我们通讯录里面的名片。虽然不是一条显示全部，但每个名字每个电话，每个E-mail都有，而且都是分开显示的。现在对于这个应该就有点感觉了。

![img](http://emanual.github.io/md-android/img/device_contact/02_contact2.jpg) 

再看一下表的结构，用”.schema” 命令后会看到，类似如下的信息：
```  
.schema data
CREATE TABLE data (
_id INTEGER PRIMARY KEY AUTOINCREMENT,
package_id INTEGER REFERENCES package(_id),
mimetype_id INTEGER REFERENCES mimetype(_id) NOT NULL,
raw_contact_idINTEGER REFERENCES raw_contacts(_id) NOT NULL,
is_primary INTEGER NOT NULL DEFAULT 0,
is_super_primary INTEGER NOT NULL DEFAULT 0,
data_version INTEGER NOT NULL DEFAULT 0,

data1 TEXT,
data2 TEXT,
data3 TEXT,
data4 TEXT,
data5 TEXT,
data6 TEXT,
data7 TEXT,
data8 TEXT,
data9 TEXT,
data10 TEXT,
data11 TEXT,
data12 TEXT,
data13 TEXT,
data14 TEXT,
data15 TEXT,

data_sync1 TEXT,
data_sync2 TEXT,
data_sync3 TEXT,
data_sync4 TEXT );
```
下面还有点索引和触发器的信息就不看了，结合查询的数据看一下。其中“_id”就是表的一个自增id字段。第二个package_id暂时没用到，数据里面全是空。第三个字段minetype_id应该就是MIMETYPE了(其实还是有点不一样的)。后的raw_contact_id就是名片的ID。再看后的data1，data2等字段，每条数据中的这几项都不大相同，准确的说，minetype_id字段不同的数据data1,data2 等字段的数据就不同。
现在应该就有一个概念了，以前说的MIMETYPE的值确定Data.DATA1等的值的类型的意思就是在data数据库中通过mimetype_id的值就可以确定data1,data2等字段的真正意义。也就是说在data数据库中通过minetype_id的值可以确定那一条数据到底是存储的姓名，还是电话，还是E-mail或者其它。
这样一来，如果我们要查询某个特定的数据的时候就可以直接查询data表里面的data1，data2这类字段的值，而唯一的必要条件就是在where条件语句里面将minetype_id赋为对应的值。这样就有了一个统一的数据访问方法。而且可以通过这个表查到所以想要的数据。
所以如果想要通过姓名查找一个人的电话就可以这样了，先通过设置MIMETYPE 为
```  
ContactsContract.CommonDataKinds.StructuredName.DISPLAY_NAME 
//查找姓名所对应的 RAW_CONTACT_ID 。再将MIMETYPE 设置为
ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE
//查找上面找到的 RAW_CONTACT_ID 所对应的电话就可以了。
```