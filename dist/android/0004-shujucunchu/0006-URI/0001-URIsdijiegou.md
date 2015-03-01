因为content provider响应到来的URIs，所以我们基于这一点比较了下content provider和web site。所以，想要从一个content provider里面获取数据，你所要做的就是调用一个URI。不过，从content provider返回的数据，是由android指针对象所代表的行和列形式的数据。在下面，我们就要来查看下你可以用来获取数据的URI的结构。
Android里面的Content URIs看起来像HTTP的URLs，除了它们是以content作为开始的一般形式：
```  
Content://*/*/*
```
这里有一个URI的示例，在notes的一个数据库里识别号码为23的note：
```  
Content://com.google.provider.NotePad/notes/23
```
在content:之后，URI包含了对authority的唯一识别，它用来在provider注册里面定位此provider。在这个例子当中，Com.google.provider.NotePad就是URI中authority的部分。
/notes/23是具体到某一个provider的路径部分。Notes和23部分叫做路径字段。Provider的任务就是来处理和解释这些路径字段。
Content provider的开发者通常通过在Java类里面或者这个provider部署包的Java接口中声明常量来定义路径。除此之外，路径的第一部分可能指向对象的一个集合。举例来说，/notes指向了notes集合或者目录，而/23指向了具体的note项目。
有了这个URI，provider就会获取URI所指示的记录。Provider还可以通过URI的可变内容来使用改变状态的方法：insert，updates或者delete。