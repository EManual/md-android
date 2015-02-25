SQLite与其他常见的DBMS的最大不同是它对数据类型的支持。其他常见的DBMS通常支持强类型的数据，也就是每一列的类型都必须预先指定，但是SQLite采用的是弱类型的字段。实际上，其内部仅有下列五种存储类型：
```  
NULL: 表示一个NULL值。
INTEGER: 用来存储一个整数,根据大小可以使用1,2,3,4,6,8位来存储。
REAL: IEEE 浮点数。
TEXT: 按照字符串来存储。
BLOB: 按照二进制值存储，不做任何改变。
```
要注意,这些类型是值本身的属性,而不是列的属性。
但是为了和其他DBMS（以及SQL标准）兼容，在其create table语句中可以指定列的类型，为此,SQLite有个列相似性的概念(Column Affinity). 列相似性是列的属性,SQLite有以下几种列相似性:
```  
TEXT: TEXT列使用NULL,TEXT或者BLOB存储任何插入到此列的数据,如果数据是数字,则转换为TEXT。
NUMERIC: NUMERIC列可以使用任何存储类型，它首先试图将插入的数据转换为REAL或INTEGER型的，如果成功则存储为REAL和INTEGER型,否则不加改变的存入。
INTEGER:和NUMERIC类似，只是它将可以转换为INTEGER值都转换为INTEGER，如果是REAL型，且没有小数部分，也转为INTEGER。
REAL: 和NUMERIC类型，只是它将可以转换为REAL和INTEGER值都转换为REAL。
NONE:不做任何改变的尝试。
```
SQLite根据create table语句来决定每个列的列相似性.规则如下(大小写均忽略):
1. 如果数据类型中包括INT,则是INTEGER
2. 如果数据类型中包括CHAR,CLOB,TEXT则是TEXT
3. 如果数据类型中包括BLOB,或者没有指定数据类型,则是NONE
4. 如果数据类型中包括REAL,FLOA或者DOUB,则是REAL
5. 其余的情况都是NUMERIC
由上可知,对于sqlite来说char,varchar,nchar,nvarchar等都是等价的，且后面最大长度也是没有意义的。但是对于其他DBMS却不是相同的。另外，列相似性仅仅是向Sqlite提出了一个存储数据的建议，即使实际存储的数据类型和列相似性不一致，SQLite还是可以成功插入的，下面给出一个例子来说明下以上论述，注意，这个例子需要在SQLite的命令行下运行，如果在SQLite Expert工具下执行，SQLite会进行一些额外的处理。
如下图，创建一个新表，两列的类型分别是int 和varchar，但是还是可以插入其他类型的数据，并且可以正确读出。
要注意SQLite的这种特性可能会给SQLite的ADO驱动造成一些麻烦，因为.NET都是强类型的语言，必须把数据库中的字段转换为合适的类型，所以在插入数据的时候，还是应该严格的按照create table中的定义插入数据。
（2）自增列
在SQL Server中，只需要指定identity(1,1)就可以设定自增列，但是在SQLite中不支持这样做。在SQLite中，任何一张表都有一个字段类型是Integer，且是自增的，这个列是作为B树的索引的，它的名字是ROWID，test2表虽然只有一列，但是ROWID列还是存在的。在程序中对任何一张表都可以使用ROWID作为自增列。不过这样可能导致和其他数据库的不兼容，SQLite中如果一个列的声明类型是Integer，并且是主键，那么这个列的名字就成为ROWID的别名。注意，声明类型必须是Integer，而不能是int或bigint之类。
注意上面例子的最后3条语句，它显示了SQLite默认的自增列算法是在当前表中最大的数再加1，这样可能导致的结果是ID被重复使用——当最后一条数据被删除的时候。这与SQL Server的Identity列的行为是不一致的.
SQL Server会记住每一次插入的序号，哪怕它已经被删除了。要实现SQL Server这样的效果，需要使用autoincrement关键字。如下例所示：
不过autoincrement关键字不被SQL Server支持（我不知道SQL 92标准中是否有此关键字），同样SQL Server的indentity关键字在SQLite中也无法使用，因为SQLite只要求声明类型必须是integer才可以启用自增列。所以，我想不出什么方法能使建库的脚本能够不加修改的被两种数据库使用。
(3) 日期函数
Sqlite的日期函数比较有特色，它的使用本质上是调用C的库函数strftime.
(4) 不被支持的特性
用户自定义函数，存储过程
外键的约束（不过可以通过自定义触发器来替代）