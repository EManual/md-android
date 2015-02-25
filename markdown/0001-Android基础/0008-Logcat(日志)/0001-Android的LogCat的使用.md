在Eclipse中安装ADT和android sdk包之后，运行以开发的android程序时，在LogCat窗口中会显示出一系列的信息，这些信息是每一个程序通过Dalvik虚拟机所传出的实时信息，可以方便我们对程序的了解。
在log窗口中，每条信息都包含五个部分，Time,标题空白,pid,tag和Message。
#### 1、Time
表示执行的时间，这个信息对于学习生命周期，分析程序运行的先后顺序特别有用。
#### 2、标题空白的列
表示的是信息的种类，分为V,D,I,W,E五种。
V:verbose，显示全部信息
D:Debug，显示调试信息
I:Info，显示一般信息
W:Warming，显示警告信息
E:Error，显示错误信息
可以通过点击LogCat上面的用圆圈括起来的V,D,I,W,E来改变显示的范围。比如选择了W，那就只有警告信息和错误信息可以显示出来了。
#### 3、pid
表示程序运行时的进程号
#### 4、tag
标签，通常表示系统中的一些进程名，比如我们运行helloworld程序的话，就会看到activitymanager在运行。
#### 5、Message
表示进程运行时的一些具体信息，比如我们运行helloworld程序的话，就会看到starting activity...helloWorld的字样
可以输出LogCat的信息到文本文件中，以方便分析。在下拉框中选择输出选择的信息就可以了。
下面是输出到文件中的启动helloWorld程序时的一条信息的例子，分别用5个下划线标出了上面介绍的内容:
```  
05-20 15:46:10.129: INFO/ActivityManager(60): Starting activity: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=com.example.android.helloworld/.HelloWorld }
```
#### 6、Filter的使用
可以在Filter中输入筛选信息，使LogCat中只现实我们需要分析的信息。比如我们只想看和HelloWorld相关的信息，就可以在Filter中输入HelloWorld，这样只有Message中包含HelloWorld的内容才会显示出来。
#### 7、LogCat中信息不能显示
上面说了这么多关于logCat的使用，可能LogCat中根本就什么信息都没有显示！没关系，只要在Eclipse中选择window->show view->other->android->devices就可以了。
#### 8、在LogCat中输出程序的运行信息
a、在程序中导入相应的包
```  
import android.util.Log;
```
b、在需要输出信息的函数中增加相关的调试代码
```  
Log.i("hi world","oncreate");
```
方法i是Log类的静态方法，可以直接使用，我们看着各类的定义可以看到，它提供了多种输出方法，分别对应我们上面提到的V,D,I,W,E。用哪个方法就决定了输出的类型，这里用i，表示输出的是information。
这个方法中的第一个参数就是要显示在Tag那一栏的内容，把这条语句加到OnCreate方法中，执行时LogCat中就会显示如下的信息。
05-22 21:58:22.894 I3910hi worldonCreate
#### 9、创建新的Filter
有时候只想看我们程序中用Log类的相关方法输出的各种信息，这时就可以考虑新建一个过滤器。点击LogCat的右上角的“+”号，可以创建一个新的过滤器。比如我们在by Log Tag的选项中填入上面程序输出的"hi world"这个tag。这样再运行时在我们新创建的Filter中就只显示hi world这个tag标记出来的信息了。