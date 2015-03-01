通过使用一些辅助性工具来找到程序中的瓶颈，然后就可以对瓶颈部分的代码进行优化。一般有两种方案：即优化代码或更改设计方法。我们一般会选择后者，因为不去调用以下代码要比调用一些优化的代码更能提高程序的性能。而一个设计良好的程序能够精简代码，从而提高性能。 
下面将提供一些在JAVA程序的设计和编码中，为了能够提高JAVA程序的性能，而经常采用的一些方法和技巧。 
#### 1．对象的生成和大小的调整。 
JAVA程序设计中一个普遍的问题就是没有好好的利用JAVA语言本身提供的函数，从而常常会生成大量的对象（或实例）。由于系统不仅要花时间生成对象，以后可能还需花时间对这些对象进行垃圾回收和处理。因此，生成过多的对象将会给程序的性能带来很大的影响。 
例1：关于String ,StringBuffer，+和append 
JAVA语言提供了对于String类型变量的操作。但如果使用不当，会给程序的性能带来影响。如下面的语句： 
```  
String name=new String("HuangWeiFeng"); 
System.out.println(name+"is my name"); 
```
看似已经很精简了，其实并非如此。为了生成二进制的代码，要进行如下的步骤和操作： 
(1) 生成新的字符串 new String（STR_1); 
(2) 复制该字符串; 
(3) 加载字符串常量"HuangWeiFeng"（STR_2); 
(4) 调用字符串的构架器（Constructor）; 
(5) 保存该字符串到数组中（从位置0开始）; 
(6) 从java.io.PrintStream类中得到静态的out变量; 
(7) 生成新的字符串缓冲变量new StringBuffer(STR_BUF_1); 
(8) 复制该字符串缓冲变量; 
(9) 调用字符串缓冲的构架器（Constructor）; 
(10) 保存该字符串缓冲到数组中（从位置1开始）; 
(11) 以STR_1为参数，调用字符串缓冲(StringBuffer)类中的append方法; 
(12) 加载字符串常量"is my name"(STR_3); 
(13) 以STR_3为参数，调用字符串缓冲(StringBuffer)类中的append方法; 
(14) 对于STR_BUF_1执行toString命令; 
(15) 调用out变量中的println方法，输出结果。 
由此可以看出，这两行简单的代码，就生成了STR_1,STR_2,STR_3,STR_4和STR_BUF_1五个对象变量。这些生成的类的实例一般都存放在堆中。堆要对所有类的超类，类的实例进行初始化，同时还要调用类极其每个超类的构架器。而这些操作都是非常消耗系统资源的。因此，对对象的生成进行限制，是完全有必要的。 
经修改，上面的代码可以用如下的代码来替换。 
```  
StringBuffer name=new StringBuffer("HuangWeiFeng"); 
System.out.println(name.append("is my name.").toString()); 
```
系统将进行如下的操作： 
(1) 生成新的字符串缓冲变量new StringBuffer(STR_BUF_1); 
(2) 复制该字符串缓冲变量; 
(3) 加载字符串常量"HuangWeiFeng"(STR_1); 
(4) 调用字符串缓冲的构架器（Constructor）; 
(5) 保存该字符串缓冲到数组中（从位置1开始）; 
(6) 从java.io.PrintStream类中得到静态的out变量; 
(7) 加载STR_BUF_1; 
(8) 加载字符串常量"is my name"(STR_2); 
(9) 以STR_2为参数，调用字符串缓冲(StringBuffer)实例中的append方法; 
(10) 对于STR_BUF_1执行toString命令(STR_3); 
(11)调用out变量中的println方法，输出结果。 
由此可以看出，经过改进后的代码只生成了四个对象变量：STR_1,STR_2,STR_3和STR_BUF_1.你可能觉得少生成一个对象不会对程序的性能有很大的提高。但下面的代码段2的执行速度将是代码段1的2倍。因为代码段1生成了八个对象，而代码段2只生成了四个对象。 
代码段1： 
```  
String name= new StringBuffer("HuangWeiFeng"); 
name+="is my"; 
name+="name"; 
```
代码段2：
```  
StringBuffer name=new StringBuffer("HuangWeiFeng"); 
name.append("is my"); 
name.append("name.").toString(); 
```
因此，充分的利用JAVA提供的库函数来优化程序，对提高JAVA程序的性能时非常重要的.其注意点主要有如下几方面； 
#### (1) 尽可能的使用静态变量（Static Class Variables） 
如果类中的变量不会随他的实例而变化，就可以定义为静态变量，从而使他所有的实例都共享这个变量。
例： 
```  
public class foo 
{ 
	SomeObject so=new SomeObject();
} 
```
就可以定义为： 
```  
public class foo 
{ 
	static SomeObject so=new SomeObject(); 
}
```
#### (2) 不要对已生成的对象作过多的改变。 
对于一些类(如：String类)来讲，宁愿在重新生成一个新的对象实例，而不应该修改已经生成的对象实例。 
例： 
```  
String name="Huang"; 
name="Wei"; 
name="Feng";
```
上述代码生成了三个String类型的对象实例。而前两个马上就需要系统进行垃圾回收处理。如果要对字符串进行连接的操作，性能将得更差，因为系统将不得为此生成更多得临时变量，如上例1所示。 
#### (3) 生成对象时，要分配给它合理的空间和大小JAVA中的很多类都有它的默认的空间分配大小。对于StringBuffer类来讲，默认的分配空间大小是16个字符。如果在程序中使用StringBuffer的空间大小不是16个字符，那么就必须进行正确的初始化。 
#### (4) 避免生成不太使用或生命周期短的对象或变量。对于这种情况，因该定义一个对象缓冲池。以为管理一个对象缓冲池的开销要比频繁的生成和回收对象的开销小的多。 
#### (5) 只在对象作用范围内进行初始化。JAVA允许在代码的任何地方定义和初始化对象。这样，就可以只在对象作用的范围内进行初始化。从而节约系统的开销。 
例： 
```  
SomeObject so=new SomeObject(); 
if(x==1) then 
{ 
	Foo=so.getXX();
}
```
可以修改为： 
```  
if(x==1) then 
{ 
	SomeObject so=new SomeObject();
	Foo=so.getXX();
}
```
#### 2．异常(Exceptions) 
JAVA语言中提供了try/catch来发方便用户捕捉异常，进行异常的处理。但是如果使用不当，也会给JAVA程序的性能带来影响。因此，要注意以下两点： 
#### (1) 避免对应用程序的逻辑使用try/catch 
如果可以用if,while等逻辑语句来处理，那么就尽可能的不用try/catch语句。 
#### (2) 重用异常 
在必须要进行异常的处理时，要尽可能的重用已经存在的异常对象。以为在异常的处理中，生成一个异常对象要消耗掉大部分的时间。 
#### 3. 线程(Threading) 
一个高性能的应用程序中一般都会用到线程。因为线程能充分利用系统的资源。在其他线程因为等待硬盘或网络读写而 时，程序能继续处理和运行。但是对线程运用不当，也会影响程序的性能。 
例2：正确使用Vector类 
Vector 主要用来保存各种类型的对象（包括相同类型和不同类型的对象）。但是在一些情况下使用会给程序带来性能上的影响。这主要是由Vector类的两个特点所决定的。第一，Vector提供了线程的安全保护功能。即使Vector类中的许多方法同步。但是如果你已经确认你的应用程序是单线程，这些方法的同步就完全不必要了。第二，在Vector查找存储的各种对象时，常常要花很多的时间进行类型的匹配。而当这些对象都是同一类型时，这些匹配就完全不必要了。因此，有必要设计一个单线程的，保存特定类型对象的类或集合来替代Vector类.用来替换的程序如下（StringVector.java）： 
```  
public class StringVector {
	private String[] data;
	private int count;
	public StringVector() {
		this(10); // default size is 10
	}
	public StringVector(int initialSize) {
		data = new String[initialSize];
	}
	public void add(String str) {
		// ignore null strings
		if (str == null) {
			return;
		}
		ensureCapacity(count + 1);
		data[count++] = str;
	}
	private void ensureCapacity(int minCapacity) {
		int oldCapacity = data.length;
		if (minCapacity > oldCapacity) {
			String oldData[] = data;
			int newCapacity = oldCapacity * 2;
			data = new String[newCapacity];
			System.arraycopy(oldData, 0, data, 0, count);
		}
	}
	public void remove(String str) {
		if (str == null) {
			return;// ignore null str }
			for (int i = 0; i < count; i++) {
				// check for a match
				if (data[i].equals(str)) {
					System.arraycopy(data, i + 1, data, i, count - 1); // copy
																		// data
					// allow previously valid array element be gc′d
					data[--count] = null;
					return;
				}
			}
		}
	}
	public final String getStringAt(int index) {
		if (index < 0) {
			return null;
		} else if (index > count) {
			return null;
		}// index is >  strings }
		else {
			return data[index]; // index is good }
		}
	}
}
```
因此，代码： 
```  
Vector Strings=new Vector(); 
Strings.add("One"); 
Strings.add("Two"); 
String Second=(String)Strings.elementAt(1);  
```
可以用如下的代码替换： 
```  
StringVector Strings=new StringVector(); 
Strings.add("One"); 
Strings.add("Two"); 
String Second=Strings.getStringAt(1); 
```
这样就可以通过优化线程来提高JAVA程序的性能。用于测试的程序如下（TestCollection.java）: 
```  
import java.util.Vector;
public class TestCollection {
	public static void main(String args[]) {
		TestCollection collect = new TestCollection();
		if (args.length == 0) {
			System.out.println("Usage: java TestCollection [ vector | stringvector ]");
			System.exit(1);
		}
		if (args[0].equals("vector")) {
			Vector store = new Vector();
			long start = System.currentTimeMillis();
			for (int i = 0; i < 1000000; i++) {
				store.addElement("string");
			}
			long finish = System.currentTimeMillis();
			System.out.println((finish - start));
			start = System.currentTimeMillis();
			for (int i = 0; i < 1000000; i++) {
				String result = (String) store.elementAt(i);
			}
			finish = System.currentTimeMillis();
			System.out.println((finish - start));
		} else if (args[0].equals("stringvector")) {
			StringVector store = new StringVector();
			long start = System.currentTimeMillis();
			for (int i = 0; i < 1000000; i++) {
				store.add("string");
			}
			long finish = System.currentTimeMillis();
			System.out.println((finish - start));
			start = System.currentTimeMillis();
			for (int i = 0; i < 1000000; i++) {
				String result = store.getStringAt(i);
			}
			finish = System.currentTimeMillis();
			System.out.println((finish - start));
		}
	}
}
```
关于线程的操作，要注意如下几个方面： 
#### (1) 防止过多的同步 
如上所示，不必要的同步常常会造成程序性能的下降。因此，如果程序是单线程，则一定不要使用同步。 
#### (2) 同步方法而不要同步整个代码段 
对某个方法或函数进行同步比对整个代码段进行同步的性能要好。 
#### (3) 对每个对象使用多”锁”的机制来增大并发。 
一般每个对象都只有一个”锁”，这就表明如果两个线程执行一个对象的两个不同的同步方法时，会发生”死锁”。即使这两个方法并不共享任何资源。为了避免这个问题，可以对一个对象实行”多锁”的机制。如下所示： 
```  
class foo {
	private static int var1;
	private static Object lock1 = new Object();
	private static int var2;
	private static Object lock2 = new Object();
	public static void increment1() {
		synchronized (lock1) {
			var1++;
		}
	}
	public static void increment2() {
		synchronized (lock2) {
			var2++;
		}
	}
}
```
#### 4.输入和输出(I/O) 
输入和输出包括很多方面，但涉及最多的是对硬盘，网络或数据库的读写操作。对于读写操作，又分为有缓存和没有缓存的；对于数据库的操作，又可以有多种类型的JDBC驱动器可以选择。但无论怎样，都会给程序的性能带来影响。因此，需要注意如下几点： 
#### (1) 使用输入输出缓冲 
尽可能的多使用缓存。但如果要经常对缓存进行刷新（flush），则建议不要使用缓存。 
#### (2) 输出流(Output Stream)和Unicode字符串 
当时用Output Stream和Unicode字符串时，Write类的开销比较大。因为它要实现Unicode到字节(byte)的转换.因此，如果可能的话,在使用Write类之前就实现转换或用OutputStream类代替Writer类来使用。 
#### (3) 当需序列化时使用transient 
当序列化一个类或对象时，对于那些原子类型（atomic）或可以重建的原素要表识为transient类型。这样就不用每一次都进行序列化。如果这些序列化的对象要在网络上传输，这一小小的改变对性能会有很大的提高。 
#### (4) 使用高速缓存（Cache） 
对于那些经常要使用而又不大变化的对象或数据，可以把它存储在高速缓存中。这样就可以提高访问的速度。这一点对于从数据库中返回的结果集尤其重要。 
#### (5) 使用速度快的JDBC驱动器（Driver） 
JAVA对访问数据库提供了四种方法。这其中有两种是JDBC驱动器。一种是用JAVA外包的本地驱动器；另一种是完全的JAVA驱动器。具体要使用哪一种得根据JAVA布署的环境和应用程序本身来定。 
#### 5.一些其他的经验和技巧 
(1) 使用局部变量。 
(2) 避免在同一个类中动过调用函数或方法(get或set)来设置或调用变量。 
(3) 避免在循环中生成同一个变量或调用同一个函数（参数变量也一样）。 
(4) 尽可能的使用static,final,private等关键字。 
(5) 当复制大量数据时，使用System.arraycopy()命令。 