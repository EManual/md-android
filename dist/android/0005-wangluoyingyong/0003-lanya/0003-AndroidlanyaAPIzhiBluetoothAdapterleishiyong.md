使用BluetoothAdapter类，你能够在Android设备上查找周边的蓝牙设备然后配对(绑定)，蓝牙通讯是基于唯一地址MAC来相互传输的，考虑到安全问题Bluetooth通讯时需要先配对。然后开始相互连接，连接后设备将会共享同一个RFCOMM通道以便相互传输数据，目前这些实 现在Android 2.0或更高版本SDK上实现。
一、查找发现 findding/discovering devices
对于Android查找发现蓝牙设备使用BluetoothAdapter类的startDiscovery()方法就可以执行一个异步方式获取周边的蓝牙设备，因为是一个异步的方法所以我们不需要考虑线程被阻塞问题，整个过程大约需要12秒时间，这时我们紧接着注册一个BroadcastReceiver对象来接收查找到的蓝牙设备信息，我们过滤ACTION_FOUND这个Intent动作来获取每个远程设备的详细信息，通过附加参数在Intent字段EXTRA_DEVICE和EXTRA_CLASS,中包含了每个BluetoothDevice对象和对象的该设备类型 BluetoothClass ，示例代码
```  
private final BroadcastReceiver cwjReceiver = new BroadcastReceiver() {
	public void onReceive(Context context, Intent intent) {
		String action = intent.getAction();
		if (BluetoothDevice.ACTION_FOUND.equals(action)) {
			BluetoothDevice device = intent
					.getParcelableExtra(BluetoothDevice.EXTRA_DEVICE);
			myArrayAdapter.add(device.getName() + " android123
					+ device.getAddress()); // 获取设备名称和mac地址
		}
	}
};
// 注册这个 BroadcastReceiver
IntentFilter filter = new IntentFilter(BluetoothDevice.ACTION_FOUND);
registerReceiver(cwjReceiver, filter);
```
记住在Service或Activity中重写onDestory()方法，使用unregisterReceiver方法反注册这个BroadcastReceiver对象保证资源被正确回收。
一、些其他的状态变化有 
ACTION_SCAN_MODE_CHANGED 额外参数 EXTRA_SCAN_MODE 和 EXTRA_PREVIOUS_SCAN_MODE以及SCAN_MODE_CONNECTABLE_DISCOVERABLE、 SCAN_MODE_CONNECTABLE和SCAN_MODE_NONE,
二、配对绑定 bnded/paired device
在Android中配对一个蓝牙设备可以调用BluetoothAdapter类的getBondedDevices()方法可以获取已经配对的设备，该方法将会返回一个BluetoothDevice数组来区分每个已经配对的设备，示例代码如下:
```  
Set<BluetoothDevice> pairedDevices = cwjBluetoothAdapter
				.getBondedDevices();
if (pairedDevices.size() > 0) // 如果获取的结果大于0，则开始逐个解析
{
	for (BluetoothDevice device : pairedDevices) {

		myArrayAdapter.add(device.getName() + " eoeandroid
				+ device.getAddress());
		// 获取每个设备的名称和MAC地址添加到数组适配器myArrayAdapter中。
	}
}
```
三、允许发现 enabling discoverability
如果需要用户确认操作，不需要获取底层蓝牙服务实例，可以通过一个Intent来传递ACTION_REQUEST_DISCOVERABLE参 数，这里通过startActivityForResult来强制获取一个结果，重写startActivityForResult()方法获取执行结果，返回结果有RESULT_OK和RESULT_CANCELLED分别代表开启和取消(失败)，当然最简单的方法是直接执行，示例代码如下
```  
Intent cwjIntent = new Intent(BluetoothAdapter.ACTION_REQUEST_DISCOVERABLE);
cwjIntent.putExtra(BluetoothAdapter.EXTRA_DISCOVERABLE_DURATION, 300);
startActivity(cwjIntent);
```