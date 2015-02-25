以前我们都提到了有关Android平台蓝牙的配对、发现、启用等操作，本文开始通过BluetoothSocket类建立有关蓝牙通讯的套接字。从Android 2.0开始支持这一特性，蓝牙和LAN一样通过MAC地址来识别远程设备，建立完通讯连接RFCOMM通道后以输入、输出流方式通讯。
#### 一、连接设备
蓝牙通讯分为server服务器端和client客户端，它们之间使用BluetoothSocket 类的不同方法来获取数据。
1. 作为服务器
如果一个设备需要和两个或多个设备连接时，就需要作为一个server来传输，在android中提供了BluetoothServerSocket类来处理用户发来的信息，服务器端套接字在接受(accepted) 一个客户发来的BluetoothSocket时作出相应的响应。示例代码如下:
```  
private class AcceptThread extends Thread {
	private final BluetoothServerSocket cwjServerSocket;
	public AcceptThread() {
		BluetoothServerSocket tmp = null; // 使用一个临时对象代替，因为cwjServerSocket定义为final
		try {
			tmp = myAdapter.listenUsingRfcommWithServiceRecord(NAME,
					CWJ_UUID); // 服务仅监听
		} catch (IOException e) {
		}
		cwjServerSocket = tmp;
	}
	public void run() {
		BluetoothSocket socket = null;
		while (true) { // 保持连接直到异常发生或套接字返回
			try {
				socket = cwjServerSocket.accept(); // 如果一个连接同意
			} catch (IOException e) {
				break;
			}
			if (socket != null) {
				manageConnectedSocket(socket); // 管理一个已经连接的RFCOMM通道在单独的线程。
				cwjServerSocket.close();
				break;
			}
		}
	}
	public void cancel() { // 取消套接字连接，然后线程返回
		try {
			cwjServerSocket.close();
		} catch (IOException e) {
		}
	}
}
```
在这里android开发网提醒大家需要注意的是服务器一般处理多个任务不嫩阻塞，必须使用异步的方法这里我们开了一个线程，目前Android的虚拟机上层没有提供I/O模型，这里我们以后会讲解高负载情况下性能优化解决方案。
#### 2. 作为客户端
以便初始化一个连接到远程设备，首先必须获取本地的BluetoothDevice对象，相关的方法在我们Android蓝牙API之BluetoothAdapter类 的两篇文章中有讲到，这里不再赘述，相关的示例代码如下:
```  
private class ConnectThread extends Thread {
	private final BluetoothSocket cwjSocket;
	private final BluetoothDevice cwjDevice;
	public ConnectThread(BluetoothDevice device) {
		BluetoothSocket tmp = null;
		cwjDevice = device;
		try {
			tmp = device.createRfcommSocketToServiceRecord(CWJ_UUID); // 客户端创建
		} catch (IOException e) {
		}
		cwjSocket = tmp;
	}
	public void run() {
		myAdapter.cancelDiscovery(); // 取消发现远程设备，这样会降低系统性能
		try {
			cwjSocket.connect();
		} catch (IOException connectException) {
			try {
				cwjSocket.close();
			} catch (IOException closeException) {
			}
			return;
		}
		manageConnectedSocket(cwjSocket); // 管理一个已经连接的RFCOMM通道在单独的线程。
	}
	public void cancel() {
		try {
			cwjSocket.close();
		} catch (IOException e) {
		}
	}
}
```
经过上面的介绍我们可以看到在Android平台上使用蓝牙通讯相对比较方便和简单，有关数据的具体通讯我们将在下次Android蓝牙API之BluetoothSocket类(2) 讲到manageConnectedSocket的具体实现。
通过前几次的讲解，很多网友相信对Android蓝牙相关开发可以很好的掌握了，通过BluetoothServerSocket可以方便的创建一个蓝牙服务器，使用BluetoothSocket类可以很好的处理连接，今天我们继续上次的内容说下Android下如何管理蓝牙套接字的连接，今天仍然使用BluetoothSocket类，处理具体的数据流。
在Java上处理数据流很简单，提供了InputSream、OutputSream和字节数组的之间的转化。今天eoeandroid将和大家一起说下处理上次遗留的manageConnectedSocket方法的细节，由于蓝牙传输中可能存在中断，所以为了防止阻塞需要开一个工作者线程，相关的示例代码
```  
private class ConnectedThread extends Thread {
	private final BluetoothSocket cwjSocket;
	private final InputStream cwjInStream;
	private final OutputStream cwjOutStream;
	public ConnectedThread(BluetoothSocket socket) {
		cwjSocket = socket;
		InputStream tmpIn = null; // 上面定义的为final这是使用temp临时对象
		OutputStream tmpOut = null;
		try {
			tmpIn = socket.getInputStream(); // 使用getInputStream作为一个流处理
			tmpOut = socket.getOutputStream();
		} catch (IOException e) {
		}
		cwjInStream = tmpIn;
		cwjOutStream = tmpOut;
	}
	public void run() {
		byte[] buffer = new byte[1024];
		int bytes;
		while (true) {
			try {
				bytes = cwjInStream.read(buffer);
				mHandler.obtainMessage(MESSAGE_READ, bytes, -1, buffer)
						.sendToTarget(); // 传递给UI线程
			} catch (IOException e) {
				break;
			}
		}
	}
	public void write(byte[] bytes) {
		try {
			cwjOutStream.write(bytes);
		} catch (IOException e) {
		}
	}
	public void cancel() {
		try {
			cwjSocket.close();
		} catch (IOException e) {
		}
	}
}
```
对于具体的连接，我们看到在Android平台上使用了Java标准的输入、输出流操作，BluetoothSocket提供的getInputStream()和getOutputStream()方法可以很好的处理我们具体的数据。