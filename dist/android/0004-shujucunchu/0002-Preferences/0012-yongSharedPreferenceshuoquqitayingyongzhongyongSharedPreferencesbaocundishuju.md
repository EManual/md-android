1．创建一个要获取的那个应用的上下文
```  
Context context = createPackageContext(packageName,Context.CONTEXT_IGNORE_SECURITY);
```  
packageName:要访问的应用的包名。
2．获得SharedPreferences
```  
SharedPreferences share = context.getSharedPreferences(name, Context.MODE_WORLD_READABLE+Context.MODE_WORLD_WRITEABLE);
```  
Name:要访问的sharedPreferences保存的文件名
#### 从一个包中的Avtivity创建另外另外一个包的Context
Android中有Context的概念，想必大家都知道。Context可以做很多事情，打开activity、发送广播、打开本包下文件夹和数据库、获取classLoader、获取资源等等。如果我们得到了一个包的Context对象，那我们基本上可以做这个包自己能做的大部分事情。
那我们能得到吗？很高兴的告诉你，能！
Context有个createPackageContext方法，可以创建另外一个包的上下文，这个实例不同于它本身的Context实例，但是功能是一样的。
这个方法有两个参数：
1、packageName  包名，要得到Context的包名。
2、flags标志位，有CONTEXT_INCLUDE_CODE和CONTEXT_IGNORE_SECURITY两个选项。CONTEXT_INCLUDE_CODE的意思是包括代码，也就是说可以执行这个包里面的代码。CONTEXT_IGNORE_SECURITY的意思是忽略安全警告，如果不加这个标志的话，有些功能是用不了的，会出现安全警告。
下面给个小例子，执行另外一个包里面的某个类的方法。
另外一个包的包名是chroya.demo,类名Main，方法名print，代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
class Main extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
	}
	public void print(String msg) {
		Log.d("Main", "msg:" + msg);
	}
}
```
本包的调用Main的print方法的代码块如下：
```  
Context c = createPackageContext("chroya.demo",
		Context.CONTEXT_INCLUDE_CODE | Context.CONTEXT_IGNORE_SECURITY);
// 载入这个类
Class clazz = c.getClassLoader().loadClass("chroya.demo.Main");
// 新建一个实例
Object owner = clazz.newInstance();
// 获取print方法，传入参数并执行
Object obj = clazz.getMethod("print", String.class).invoke(owner, "Hello");
Context c = createPackageContext("chroya.demo",
		Context.CONTEXT_INCLUDE_CODE | Context.CONTEXT_IGNORE_SECURITY);
```
ok，这样，我们就调用了chroya.demo包的Main类的print方法，执行结果，打印出了Hello。 
