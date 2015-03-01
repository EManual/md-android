Android webservice 见解 
现在大多数写关于android网络webservice会用到一个接口ksoap2.0
ksoap2.0接口介绍
org.ksoap2.SoapEnvelope
org.ksoap2.SoapSerializationEnvelope
org.ksoap2.SoapObject
org.ksoap2.transport. HttpTransport
ksoap2.0接口介绍
服务端给客户端的响应数据一般有如下两种 xml或json ,不过不知道ksoap和webservice是不是json 可能是复杂对象吧
ksoap和webservice传递字符串等的比较简单 ,主要是复杂对象,客户端软件需要对复杂对象来解析 
下面是ksoap2与webservice的通讯过程
1 创建
```  
SoapObject request  = new SoapObject(serviceNamespace, methodName);
参数1是命名空间,参数2是要调用的方法的名字
request.addProperty(string,string ); //要传给服务端的参数键值，例如天气程序中 这里传递的是城市名称
```
2 封装
```  
//告诉SoapSerializationEnvelope把构造好的SoapObject封装进去：
SoapSerializationEnvelope envelope=new SoapSerializationEnvelope(SoapEnvelope.VER11);
envelope.bodyOut=sobject;
envelope.dotNet=true;
envelope.setOutputSoapObject(request);
```
******这一部分是封转你要传递的数据
3 提交并等待应答
```  
AndroidHttpTransport ht=new AndroidHttpTransport(URL);//投递SOAP数据的目标地址
ht.debug=true;
ht.call(SOAP_ACTION, envelope); //等待调用
```
4 获取结果
```  
//获取应答对象 ,复杂对象的解析
SoapObject result=(SoapObject) envelope.bodyIn;
SoapObject detail=(SoapObject) result.getProperty(String);//类似于获取服务端返回复杂节点的一个内接点String 
```
5 根据具体情况来解析复杂对象
例如:String mstr=detail.getProperty(index).toString();//detail是获取的对象,index是要获得第几个参数。