调用 WebService 分以下几步:
1、指定 WebService 的命名空间和调用方法;
2、设置调用方法的参数值，如果没有参数，可以省略，设置方法的参数值的代码如下： 
```  
rpc.addProperty("abc", "test"); 
```
要注意的是，addProperty方法的第1个参数虽然表示调用方法的参数名，但该参数值并不一定与服务端的WebService类中的方法参数名一致，只要设置参数的顺序一致即可。 
3、生成调用Webservice方法的SOAP请求信息。
```  
SoapSerializationEnvelope envelope = new SoapSerializationEnvelope(SoapEnvelope.VER11); 
envelope.bodyOut = rpc; 
envelope.dotNet = false; 
//这里如果设置为TRUE,那么在服务器端将获取不到参数值(如:将这些数据插入到数据库中的话)
envelope.setOutputSoapObject(rpc); 
```
创建SoapSerializationEnvelope对象时需要通过SoapSerializationEnvelope类的构造方法设置SOAP协议的版本号。 该版本号需要根据服务端WebService的版本号设置。 
在创建SoapSerializationEnvelope对象后，不要忘了设置SOAPSoapSerializationEnvelope类的bodyOut属性， 
该属性的值就是在第一步创建的SoapObject对象。
4、创建HttpTransportsSE对象。 
这里不要使用 AndroidHttpTransport ht = new AndroidHttpTransport(URL); 这是一个要过期的类
```  
private static String URL = "http://www.webxml.com.cn/webservices/weatherwebservice.asmx"; 
HttpTransportSE ht = new HttpTransportSE(URL); 
ht.debug = true; 
```
5、使用call方法调用WebService方法
```  
private static String SOAP_ACTION = "http://WebXml.com.cn/getWeatherbyCityName"; 
ht.call(SOAP_ACTION, envelope);
```
6、获得WebService方法的返回结果 
有两种方法： 
使用getResponse方法获得返回数据。 
使用 bodyIn 及 getProperty。
7、 这时候执行会出错，提示没有权限访问网络 需要修改 AndroidManifest.xml 文件，赋予相应权限简单来说就是增加下面这行配置：
```  
<uses-permission android:name="android.permission.INTERNET"></uses-permission>
```