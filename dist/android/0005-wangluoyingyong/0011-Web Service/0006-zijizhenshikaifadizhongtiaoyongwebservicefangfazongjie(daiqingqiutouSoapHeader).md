调用webservice总结：
1.加入第三方的jar包 Ksoap2-android-XXX。
2.访问响应的webservice的网站，查看响应的信息，得到nameSpace，methodName，url，soapAction。
3.如果request信息还有带有SoapHander的。那么就要封装：依据参数封装。
```  
Element[] header = new Element[1]; 
header[0] = new Element().createElement(nameSpace, "SoapHeader"); 
Element userName = new Element().createElement(nameSpace, "UserID"); 
userName.addChild(Node.TEXT, UserID); 
header[0].addChild(Node.ELEMENT, userName); 
Element pass = new Element().createElement(nameSpace, "PassWord"); 
pass.addChild(Node.TEXT, PassWord); 
header[0].addChild(Node.ELEMENT, pass); 
```
4.封装request信息的SoapBody
```  
// 指定WebService的命名空间和调用的方法名
SoapObject  soapObject=new SoapObject(nameSpace, methodName);
//处理soap12:Body数据部分
soapObject.addProperty("loginName",username);
soapObject.addProperty("password",password);
```
5.指定SoapSerializationEnvelope信息
```  
SoapSerializationEnvelopeenvelope=new SoapSerializationEnvelope(SoapEnvelope.VER11);
//SoapEnvelope.VER11 表示使用的soap协议的版本号 1.1 或者是1.2
envelope.headerOut=header;
envelope.bodyOut=soapObject;
envelope.dotNet = true; //指定webservice的类型的（java，PHP，dotNet）
envelope.setOutputSoapObject(soapObject); 
```
6.指定HttpTransportSE
```  
HttpTransportSE ht = new HttpTransportSE(url); 
```
7.访问webservice服务器
```  
ht.call(soapAction, envelope);
```
8.两种方式获取服务器返回的信息
```  
envelope.getResponse();
envelope.bodyIn;
```
两者的区别：Webservice开发的时候一般情况下大家接受webservice服务器返回值的时候都是使用 
SoapObject soapObject = (SoapObject) envelope.getResponse();这个来接受返回
来的值，但这种方法往往会产生java.lang.ClassCastException: org.ksoap2.
serialization.SoapPrimitive这样的错误。
在服务器端返回值是String类型的数值的时候使用SoapObject soapObject = (SoapObject) envelope.getResponse()会产生java.lang.ClassCastException: org.ksoap2.serialization.SoapPrimitive这样的错误。
使用SoapObject result = (SoapObject)envelope.bodyIn和 Object object = envelope.getResponse();就可以解决这种错误。 如果服务器返回值的类型是byte[]的时候，使用Object object = envelope.getResponse();和SoapObject result = (SoapObject)envelope.bodyIn;
都不会发生错误现象，但是在使用Object object = envelope.getResponse();取回来的值在使用base64进行解码和编码的时候会报出错误。如果使用SoapObject result = (SoapObject)envelope.bodyIn;就可以完整的将byte[]进行解码和编码，
```  
byte[] ops = Base64.decode(result.getProperty(0).toString());
SoapObject result=(SoapObject) envelope.bodyIn;
String str=result.getProperty(0).toString()；
```
或者是
```  
Object result=(Object) reqVo.envelope.getResponse();
String str=result.toString();
```
9.解析字符串str获取客户端想要的信息