Content Provider这个东西在Android平台上是最常用的共享数据的方法（似乎应用程序之间共享数据也只有这种方法吧，待求证）。虽然常用，但是这个东西要理解透彻还是要先掌握一些基础的。URI就是Content Provider（简称CP）的基础。我们要标识一个CP，就必须用URI这个东东。这就类似于我们要通过网址来标识某个特定网站，实际上网址URL本身就是一种URI。URI全称Uniform Resource Identifier, 它包括了URL和URN。而关于它们的详细解释，有心的朋友可以参考RFC3896：http://tools.ietf.org/html /rfc3986。URI不仅可以标识特定CP，还可以标识CP中特定的数据库表，就好像URL不仅可以标识特定网站，也可以标识这个网站某个特定网页一样。实际上在Android平台上URI的用途更广泛一些，它还用于Intent中data的标识。 
就Android平台而言，URI主要分三个部分：scheme, authority and path。其中authority又分为host和port。
格式如下： 
```  
scheme://host:port/path 
```
举个实际的例子： 
```  
content://com.example.project:200/folder/subfolder/etc 
\-----/  \-----------------/ \---/ \--------------------------/ 
scheme           host         port        path 
                \--------------------------------/ 
                          authority    
```
现在大家应该知道data flag中那些属性的含义了吧，看下data flag 
```  
<data android:host="string" 
      android:mimeType="string"
      android:path="string"
      android:pathPattern="string"
      android:pathPrefix="string"
      android:port="string"
      android:scheme="string" />
```
但是我们在程序中一般是不直接用URI来标识CP的，是的，正如我们通常见到的用定义的常量来标识。例如standard CP中的Contacts，我们就用Contacts.People.CONTENT_URI来标识Contacts CP中People这个表。那么要标识某个具体的人怎么办呢？这就用到了ContentUris.withAppendedId()和Uri.withAppendedPath()。例如我们要表示content://contacts/people/20，那么我们就可以用如下语句： 
```  
Uri uri = ContentUris.withAppendedId(People.CONTENT_URI, 20); 
```
或者 
```  
Uri uri = Uri.withAppendedPath(People.CONTENT_URI, "20");
```