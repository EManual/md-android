#### 一、结构
```  
public final class BluetoothServerSocket extends Object implements Closeable
java.lang.Object
android.bluetooth.BluetoothServerSocket
```
#### 二、概述
一个蓝牙监听端口。
蓝牙端口监听接口和TCP端口类似：Socket和ServerSocket类。在服务器端，使用BluetoothServerSocket类来创建一个 监听服务端口。当一个连接被BluetoothServerSocket所接受，它会返回一个新的BluetoothSocket来管理该连接。在客户 端，使用一个单独的BluetoothSocket类去初始化一个外接连接和管理该连接。
最通常使用的蓝牙端口是RFCOMM，它是被Android API支持的类型。RFCOMM是一个面向连接，通过蓝牙模块进行的数据流传输方式，它也被称为串行端口规范（Serial Port Profile，SPP）。
为了创建一个对准备好的新来的连接去进行监听BluetoothServerSocket类，使用BluetoothAdapter.listenUsingRfcommWithServiceRecord()方法。然后调用accept()方法去监听该链接的请求。在连接建立之前，该调用会被阻断，也就是说，它将返回一个BluetoothSocket类去管理该连接。每次获得该类之后，如果不再需要接受连接，最好调用在BluetoothServerSocket类下的close()方法。关闭BluetoothServerSocket类不会关 闭这个已经返回的BluetoothSocket类。
BluetoothSocket类线程安全。特别的，close()方法总会马上放弃外界操作并关闭服务器端口。
注意：需要BLUETOOTH权限。
BluetoothSocket
#### 三、公共方法
```  
public BluetoothSocket accept (int timeout) 
```
阻塞直到超时时间内的连接建立。
在一个成功建立的连接上返回一个已连接的BluetoothSocket类。
每当该调用返回的时候，它可以在此调用去接收以后新来的连接。
close()方法可以用来放弃从另一线程来的调用。
参数：
timeout （译者注：阻塞超时时间）
返回值：
已连接的 BluetoothSocket
异常：
IOException 出现错误，比如该调用被放弃，或者超时。
```  
public BluetoothSocket accept () 
```
阻塞直到一个连接已经建立。（译者注：默认超时时间设置为-1，见源码）
在一个成功建立的连接上返回一个已连接的BluetoothSocket类。
每当该调用返回的时候，它可以在此调用去接收以后新来的连接。
close()方法可以用来放弃从另一线程来的调用。
返回值：
已连接的 BluetoothSocket
异常：
IOException 出现错误，比如该调用被放弃，或者超时。
```  
public void close () 
```
马上关闭端口，并释放所有相关的资源。
在其他线程的该端口中引起阻塞，从而使系统马上抛出一个IO异常。
关闭BluetoothServerSocket不会关闭接受自accept()的任意BluetoothSocket。
异常：
　　IOException