kSOAP的运用 文档版见附件
#### 1．概述
对于Android访问远端的web Service，除了官方标准JSR 172，我们还有两种选择： 
```  
kSOAP 
Wingfoot 
```
Wingfoot是由Wingfoot Software(www.wingfoot.com)出品的一款J2ME(CLDC/CDC) SOAP1.1的轻量级实现方案。 
kSOAP是Enhydra.org的一个开源作品，是EnhydraME项目的一部分。基于Enhydra.org出品的开源通用XML解析器kXML，kSOAP完成了J2ME/MIDP平台上的SOAP解析和调用工作。 
Stefan Haustein领导的kSOAP开发小组于2001年5月17日推出了Alhpa版本。之后又经过了一年的开发，2002年6月6日推出的kSOAP 1.2支持了SOAP1.2规范。2003年8月25日推出的kSOAP2，对SOAP序列化规范支持得更好了。 
大多数人选择kSOAP的原因是，kSOAP虽然在2003年8月之后就不再维护了，但它是Open Source的，很容易加入增强特性，比如说默认情况下kSOAP2仅仅支持cmnet接入点，可以修改kSOAP2的HttpTransport.Java代码增加对cmwap接入点的支持。 
下载提示： 
kSOAP当前有两个版本：1.2和2.0。 
官方网站：http://ksoap.objectweb.org/ 
kSOAP2.0还有一个优点是，改进了对Microsoft dotNET的兼容。以前有很多人抱怨kSOAP调用dotNET编写的Web Service时遇到了不少的困扰。 
本章节我们将使用kSOAP 2.0的例子来讲解。 
为了使用kSOAP 2.0，必须还要下载工具包kXML2。 
下载提示： 
kXML当前有两个版本：1.21和2.0。 
官方网站：http://kxml.objectweb.org/ 
kXML2比kXML更小更快。 
#### 2．kSOAP2接口
让我们先熟悉一下即将用到的kSOAP2的常用接口。 
接口 
```  
org.ksoap2.SoapEnvelope 
org.ksoap2.SoapSerializationEnvelope 
org.ksoap2.SoapObject 
org.ksoap2.transport.HttpTransport 
```
SoapEnvelope对应于SOAP规范中的SOAP Envelope，封装了head和body对象。 
SoapSerializationEnvelope 是kSOAP2新增加的类，是对SoapEnvelope的扩展，对SOAP序列化(Serialization)格式规范提供了支持，能够对简单对象自 动进行序列化(simple object serialization)。而kSOAP1.x则是通过org.ksoap.ClassMap来做序列化的，不太好操作，也不利于扩展。 
SoapObject让你自如地构造SOAP调用； 
HttpTransport为你屏蔽了Internet访问/请求和获取服务器SOAP的细节。 
下面我们通过一个最简单的webservice调用，来看看kSOAP是如何做到SOAP解析的： 
#### 2.1．kSOAP和Web Service之间传递String
webservice传递String给MIDP是一件很简单的事情。首先在服务器端，不管你是用Microsft ASP.NET创建webservice，还是由tomcat+AXIS1.2支撑的webservice，都可以这么编写主服务类： 
服务器端 
```  
public class SimpleKSoapWS {
	public SimpleKSoapWS() {
	}

	public String foo(String username, String password) {
		return "fooResult";
	}
}
```
kSOAP是如何调用这个webservice的呢？ 
首先要使用SoapObject，这是一个高度抽象化的类，完成SOAP调用。可以调用它的addProperty方法填写要调用的webservice方法的参数。如下面代码所示： 
```  
SoapObject request  = new SoapObject(serviceNamespace, methodName); 
```
SoapObject构造函数的两个参数含义为： 
serviceNamespace – 你的webservice的命名空间，既可以是 
http://localhost:8088/flickrBuddy/services/Buddycast这样的，也可以是 
urn:PI/DevCentral/SoapService这样的； 
methodName – 你要调用方法的名字。 
然后，按照webservice方法参数的顺序，依次调用 
```  
request.addProperty( "username", "user" ); 
request.addProperty( "password", "pass" ); 
```
来填充webservice参数。 
注意： 
建议webservice的方法传递的参数尽量用string类型。即使是int类型，kSOAP2与Java编写的webservice也有可能交互发生异常。 
对于webservice方法返回String类型的情况，还用不着开发者做序列化(Serialization)定制工作。 
要点： 
kSOAP 1.X/2.0可以自动把四种SOAP类型映射为Java类型 
```  
SOAP type Java type 
xsd:int java.lang.Integer 
xsd:long java.lang.Long 
xsd:string java.lang.String 
xsd:boolean java.lang.Boolean 
```
除此之外，都需要开发者自己做类型映射。 
然后要告诉SoapSerializationEnvelope把构造好的SoapObject封装进去： 
```  
SoapSerializationEnvelope envelope = new SoapSerializationEnvelope(SoapEnvelope.VER11); 
envelope.bodyOut = request; 
```
要点： 
你可以通过SoapSerializationEnvelope或者SoapEnvelope的构造函数来指明你要用SOAP的哪一个规范，可以是以下几种之一： 
```  
常量SoapEnvelope.VER10：对应于SOAP 1.0规范 
常量SoapEnvelope.VER11：对应于SOAP 1.1规范 
常量SoapEnvelope.VER12：对应于SOAP 1.2规范 
```
这样，无论要调用的webservice采用了哪一个SOAP规范，你都可以轻松应对。 
接下来就要声明 
```  
HttpTransport tx = new HttpTransport(serviceURL); 
ht.debug = true; 
```
HttpTransport构造函数的参数含义为： 
serviceURL – 要投递SOAP数据的目标地址，譬如说 
http://soap.amazon.com/onca/soap3 。 
HttpTransport是一个强大的辅助类，来完成Http-call transport process，它封装了网络请求的一切，你完全不用考虑序列化消息。我们通过设置它的debug属性为true来打开调试信息。 
方法HttpTransport.call()自己就能够发送请求给服务器、接收服务器响应并序列化SOAP消息，如下所示： 
```  
ht.call(null, envelope); 
```
HttpTransport的call方法的两个参数含义为： 
soapAction – SOAP 规范定义了一个名为 SOAPAction 的新 HTTP 标头，所有 SOAP HTTP 请求（即使是空的）都必须包含该标头。 SOAPAction 标头旨在表明该消息的意图。通常可以置此参数为null，这样HttpTransport就会设置HTTP标头SOAPAction为空字符串。 
Envelope – 就是前面我们构造好的SoapSerializationEnvelope或SoapEnvelope对象。 
注意： 
对于HttpTransport的处理上，kSOAP2和kSOAP1.2的写法不一样。 
对于kSOAP 1.2，HttpTransport的构造函数是HttpTransport (String url, String soapAction)，第二个参数soapAction可以是要调用的webservice方法名。 
而kSOAP 2，构造函数是 HttpTransport(String url)。kSOAP2相当于把webservice方法名分离出去，完全交给SoapObject去封装，而HttpTransport仅仅负责把 SoapEnvelope发送出去并接收响应，这样更合理一些。 
调用call方法是一个同步过程，需要等待它返回。 
返回之后，就可以调用SoapSerializationEnvelope的getResult方法来获取结果了：
```   
Object Response = envelope.getResult(); 
```
如果HttpTransport的debug属性为true，那么此时就可以通过 
System.out.println("Response dump>>" + tx.responseDump); 
打印出HttpTransport的调试信息。尤其当前面call方法和getResult方法发生异常时，这个调试信息是非常有用的。 
前面我们的webservice方法由于是返回string，所以得到这个string值就非常简单了： 
```  
String sResponse = (String)Response; 
```
注意： 
由于HttpTransport类实际上是调用了HttpConnection作网络连接，所以必须另起一个线程来专门做kSOAP工作，否则会堵塞操作。 
综上所述，J2ME客户端的MIDlet按键事件函数这么写即可： 
MIDlet codes 
```  
import org.ksoap2.serialization.SoapObject;
import org.ksoap2.serialization.SoapSerializationEnvelope;
import org.ksoap2.transport.HttpTransport;
public class Snippet {
	public void commandAction(Command c, Displayable s) {
		if (c == exitCommand) {
			destroyApp(false);
			notifyDestroyed();
		}
		if (c == connectCommand) {
			// 匿名内部Thread，调用kSOAP2访问远程服务。
			Thread webserviceThread = new Thread() {
				public void run() {
					try {
						String serviceNamespace = "http://localhost:8080/SimpleWS/services/SimpleKSoapWS";
						String methodName = "foo";
						String serviceURL = "http://localhost:8080/SimpleWS/services/SimpleKSoapWS";
						SoapObject request = new SoapObject(serviceNamespace,
								methodName);
						request.addProperty("username", "user");
						request.addProperty("password", "pass");
						SoapSerializationEnvelope envelope = new SoapSerializationEnvelope(
								SoapEnvelope.VER11);
						envelope.bodyOut = request;
						HttpTransport tx = new HttpTransport(serviceURL);
						ht.debug = true;
						ht.call(null, envelope);
						Object Response = envelope.getResult();
						/*
						  * 必要时打印出tx.responseDump来观察soap是否正确工作
						  */
						System.out.println("dump>>" + tx.responseDump);
						String sResponse = (String) Response;
					} catch (Exception e) {
						e.printStackTrace();
					}
				}
			};
			webserviceThread.start();
		}
	}
}
```
#### 2.2．webservice返回复杂描述的情况
kSOAP2处理webservice简单的string类型返回值是很容易的。那么如何处理像亚马逊网上书店这种webservice返回的复杂描述呢？ 
kSOAP2自带了一个例子来说明，下面我们就讲解一下。 
关于亚马逊的查询书目的webservice，你可以通过http://soap.amazon.com/schemas3/AmazonWebServices.wsdl来获知定义。 
我们要关注的是它的关键词查询请求的方法，它的定义是： 
```  
<operation name="KeywordSearchRequest"> 
	<soap:operation soapAction="http://soap.amazon.com" />
	<input>
		<soap:body use="encoded"
		encodingStyle=http://schemas.xmlsoap.org/soap/encoding/ namespace="http://soap.amazon.com" />
	</input>
	<output>
		<soap:body use="encoded"
		encodingStyle=http://schemas.xmlsoap.org/soap/encoding/ namespace="http://soap.amazon.com" />
	</output>
</operation> 
```
我们提交对包含指定关键词的书目查询，如果查询成功，将会返回一系列书名节点，每一本书都提供了作者、出版社、出版日期、价格等等信息。这些书名节点都在一个“Details”节点下。查询结果的总数放在TotalResults节点。每页10个结果，可以通过查看TotalPages节点来确定需要多少页。 
那么，kSOAP2可以很简单地通过SoapObject的getProperty方法来得到书详细信息的节点，存储入一个Vector对象中，如下所示： 
```  
HttpTransport ht = new HttpTransport("http://soap.amazon.com/onca/soap3"); 
ht.call(null, envelope); 
SoapObject result = (SoapObject) envelope.getResult(); 
Vector resultVector = (Vector) result.getProperty("Details"); 
```
Vector对象中实际上还是存储了一组SoapObject对象，这里的每一个SoapObject对象对应于一本书的DOM对象。 
那么如何得到每一本书的书名、价格呢？ 
```  
for(int i = 0; i < resultVector.size(); i++){ 
	SoapObject detail = (SoapObject) resultVector.elementAt(i);
	System.out.println("书名>>"+(String) detail.getProperty("ProductName")); 
	System.out.println("日期>>"+(String) detail.getProperty("ReleaseDate")); 
	System.out.println("价格>>"+(String) detail.getProperty("ListPrice")); 
} 
```
这样就可以了。 
需要注意的是，要测试这个工程，必须到亚马逊的http://www.amazon.com/webservice 注册获取Access Key ID，也就是webservice方法中的“devtag”参数所需要的Developer-Tag。 
#### 2.3．webservice传递自定义复杂对象
下面我们讲述如何在MIDP设备和webservice之间传递自定义类，比如这个类中不但有String类型成员变量，还有Vector之类的复杂类型。 
大致思路就是，在服务器端将类实例按照一定规格（一个一个的成员变量写）序列化为byte[]，将这个byte[]数组返回给kSOAP2。kSOAP2收到之后，再反序列化，将byte[]一段一段地读入类实例。 
#### 2.3.1．webservice服务器端的做法
我们先来定义要传递的wsTeam类： 
类定义
```  
public class wsTeam{ 
	private String wsReturnCode; 
	private String wsPersonCount; 
	public StringVector wsvPersonName; 
	public byte[] serialize(); 
	public static wsTeam deserialize(byte[] data); 
}
```
其中，StringVector类是另外一个自定义类，就是简单地把String[]封装了一下，便于操作。StringVector类定义在示范代码中可以找到。 
服务器端主要是序列化，所以我们来讲讲wsTeam的serialize()函数。 
wsTeam的序列化函数
```  
public byte[] serialize() {
	ByteArrayOutputStream baos = new ByteArrayOutputStream();
	DataOutputStream dos = new DataOutputStream(baos);
	try {
		dos.writeUTF(wsReturnCode);
		dos.writeUTF(wsPersonCount);
		wsvPersonName.writeObject(dos);
		baos.close();
		dos.close();
	} catch (Exception exc) {
		exc.printStackTrace();
	}
	return baos.toByteArray();
}
```
这样，类实例就可以把自己序列化为byte[]数组。 
那么，webservice可以这么提供： 
服务器端 
```  
public class SimpleKSoapWS { 
	public SimpleKSoapWS () { 
	} 
	public byte[] foo2(String username, String password) { 
		wsTeam obj= new wsTeam ();
		return obj.serialize(); 
	} 
} 
```
到了MIDP设备上，要能够从byte[]恢复出wsTeam类实例才行。 
StringVector的序列化方法writeObject也很简单，先写入字符串数组的大小，然后再将每一个元素写入，如下所示： 
StringVector的序列化 
```  
public class StringVector {
	public synchronized void writeObject(java.io.DataOutputStream s)
			throws java.io.IOException {
		// Write out array length
		s.writeInt(count);
		// Write out all elements in the proper order.
		for (int i = 0; i < count; i++) {
			s.writeUTF(data);
		}
	}
}
```
#### 2.3.2．MIDP设备的做法
和前面的MIDlet代码差不多，只不过要kSOAP2的MarshalBase64出场了。 
在kSOAP中，我们用Base64把二进制流编码为ASCII字符串，这样就可以通过XML/SOAP传输二进制数据了。 
org.ksoap2.serialization.MarshalBase64的目的就是，把SOAP XML中的xsd:based64Binary元素序列化为Java字节数组(byete array)类型。类似的，kSOAP2还提供了MarshalDate、MarshalHashtable类来把相应的元素序列化为Java的Date、Hashtable类型。 
使用MarshalBase64 
```  
import org.ksoap2.serialization.MarshalBase64; 
SoapObject request = new SoapObject(serviceNamespace, methodName); 
SoapSerializationEnvelope envelope = 
new SoapSerializationEnvelope(SoapEnvelope.VER11); 
envelope.bodyOut = request; 
new MarshalBase64().register(envelope); 
HttpTransport tx = new HttpTransport(serviceNamespace); 
tx.debug = true; 
tx.call(null, envelope); 
Object Response = envelope.getResult(); 
```
将接收到的SoapObject强制转换为byte[]。 
转换
```  
byte[] by = (byte[])Response; 
System.out.println("succ convert!"); 
```
然后，再调用反序列化 
```  
wsTeam wc = wsTeam.deserialize(by); 
```
这样，在无线设备上就得到了wsTeam类实例了。 
wsTeam的deserialize函数是这么定义的： 
wsTeam的反序列化函数 
```  
public class StringVector {
	public static wsTeam deserialize(byte[] data) {
		ByteArrayInputStream bais = new ByteArrayInputStream(data);
		DataInputStream dis = new DataInputStream(bais);
		wsTeam wc = new wsTeam();
		try {
			wc.wsReturnCode = dis.readUTF();
			wc.wsPersonCount = dis.readUTF();
			wc.wsvPersonName.readObject(dis);
			bais.close();
			dis.close();
		} catch (Exception exc) {
			exc.printStackTrace();
		}
		return wc;
	}
}
```
StringVector的反序列化方法readObject也很简单，先读入字符串数组的大小，就自行新建一个同样大小的字符串数组，然后再将每一个元素写入这个数组，如下所示： 
StringVector的反序列化 
```  
public class StringVector {
	public synchronized void readObject(java.io.DataInputStream s)
			throws java.io.IOException, ClassNotFoundException {
		// Read in array length and allocate array
		int arrayLength = s.readInt();
		data = new String[arrayLength];
		// 同步data的大小
		count = arrayLength;
		// Read in all elements in the proper order.
		for (int i = 0; i < arrayLength; i++) {
			data = s.readUTF();
		}
	}
}
```
通过上面的反序列化，我们就可以通过
```  
for (int i=0; i < wc.wsvPersonName.size(); i++) { 
	System.out.println("第" + i +"个人：" + 
	wc.wsvPersonName.getStringAt(i));
}
```
来打印MIDlet上收到的类对象中的StringVector成员变量了。 
#### 3．小结
利用kSOAP2提供的框架，你可以在无线设备和Internet webservice之间，既可以传递简单的数值，也可以传递各种各样的类对象。