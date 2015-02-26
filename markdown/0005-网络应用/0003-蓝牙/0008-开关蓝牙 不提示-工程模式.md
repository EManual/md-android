代码如下：
```  
 /**
	  * 获得蓝牙的状态
	  */
private int getBluetoothState() {
	blueAdapter = BluetoothAdapter.getDefaultAdapter();
	if (blueAdapter.isEnabled()) {
		return STATE_ENABLED;// 开启
	} else {
		return STATE_DISABLED;// 关闭
	}
}
 /**
  * 蓝牙开关
  */
private void toggleBluetooth() {
	int state = getBluetoothState();
	if (state == STATE_DISABLED) {
		blueAdapter.enable();// 开启蓝牙
	} else {
		blueAdapter.disable();// 关闭蓝牙
	}
}
```