Android对于蓝牙开发从2.0版本的sdk才开始支持，而且模拟器不支持，测试至少需要两部手机，所以制约了很多技术人员的开发。
首先，要操作蓝牙，先要在AndroidManifest.xml里加入权限
```  
<uses-permission android:nameusespermission android:name="android.permission.BLUETOOTH_ADMIN" /> 
<uses-permission android:nameuses-permission android:name="android.permission.BLUETOOTH" />
```
然后，看下api，Android所有关于蓝牙开发的类都在android.bluetooth包下，如下图，只有8个类
而我们需要用到了就只有几个而已：
1.BluetoothAdapter 顾名思义，蓝牙适配器，直到我们建立bluetoothSocket连接之前，都要不断操作它
BluetoothAdapter里的方法很多，常用的有以下几个：
```  
cancelDiscovery()根据字面意思，是取消发现，也就是说当我们正在搜索设备的时候调用这个方法将不再继续搜索
disable()关闭蓝牙
enable()打开蓝牙，这个方法打开蓝牙不会弹出提示，更多的时候我们需要问下用户是否打开，以下这两行代码同样是打开蓝牙，不过会提示用户：
Intemtenabler=new Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE);   
startActivityForResult(enabler,reCode);//同startActivity(enabler);
getAddress()获取本地蓝牙地址
getDefaultAdapter()获取默认BluetoothAdapter，实际上，也只有这一种方法获取BluetoothAdapter
getName()获取本地蓝牙名称
getRemoteDevice(String address)根据蓝牙地址获取远程蓝牙设备
getState()获取本地蓝牙适配器当前状态（感觉可能调试的时候更需要）
isDiscovering()判断当前是否正在查找设备，是返回true
isEnabled()判断蓝牙是否打开，已打开返回true，否则，返回false
listenUsingRfcommWithServiceRecord(String name,UUID uuid)根据名称，UUID创建并返回   BluetoothServerSocket，这是创建BluetoothSocket服务器端的第一步
startDiscovery()开始搜索，这是搜索的第一步
```
2.BluetoothDevice看名字就知道，这个类描述了一个蓝牙设备
createRfcommSocketToServiceRecord(UUIDuuid)根据UUID创建并返回一个BluetoothSocket
这个方法也是我们获取BluetoothDevice的目的——创建BluetoothSocket
这个类其他的方法，如getAddress(),getName(),同BluetoothAdapter
3.BluetoothServerSocket如果去除了Bluetooth相信大家一定再熟悉不过了，既然是Socket，方法就应该都差不多，
这个类一种只有三个方法
两个重载的accept(),accept(inttimeout)两者的区别在于后面的方法指定了过时时间，需要注意的是，执行这两个方法的时候，直到接收到了客户端的请求（或是过期之后），都会阻塞线程，应该放在新线程里运行！
还有一点需要注意的是，这两个方法都返回一个BluetoothSocket，最后的连接也是服务器端与客户端的两个BluetoothSocket的连接
close()这个就不用说了吧，翻译一下——关闭！
4.BluetoothSocket,跟BluetoothServerSocket相对，是客户端
一共5个方法，不出意外，都会用到：
```  
close() 关闭
connect()连接
getInptuStream()获取输入流
getOutputStream()获取输出流
getRemoteDevice()获取远程设备，这里指的是获取bluetoothSocket指定连接的那个远程蓝牙设备
```