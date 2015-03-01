先是写个
```  
<receiver android:name=".broadcast.PairingRequest" >
    <intent-filter>
        <action android:name="android.bluetooth.device.action.PAIRING_REQUEST" />
        <action android:name="android.bluetooth.device.action.PAIRING_CANCEL" />
    </intent-filter>
</receiver>
```
然后是你的
```  
public class PairingRequest extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
		if (intent.getAction().equals("ACTION_PAIRING_REQUEST")) {
			BluetoothDevice device = intent
					.getParcelableExtra(BluetoothDevice.EXTRA_DEVICE);
			byte[] pinBytes = BluetoothDevice.convertPinToBytes("1234");
			device.setPin(pinBytes);
		}
	}
}
```
其中的蓝牙BluetoothDevice这个类要用源码里的替换下（不然会缺少方法）。