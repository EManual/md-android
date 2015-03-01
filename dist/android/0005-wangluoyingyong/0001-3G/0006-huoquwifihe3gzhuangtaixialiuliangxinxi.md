代码片段：
```  
IBatteryStats battryStats = IBatteryStats.Stub
	.asInterface(ServiceManager.getService("batteryinfo"));
byte[] data = null;
try {
	data = battryStats.getStatistics();
	Parcel parcel = Parcel.obtain();
	parcel.unmarshall(data, 0, data.length);
	parcel.setDataPosition(0);
	final BatteryStatsImpl impl = BatteryStatsImpl.CREATOR
			.createFromParcel(parcel);
	try {
		Thread.sleep(1000);
	} catch (InterruptedException e) {
		e.printStackTrace();
	}
	long length1_1 = impl
			.getTotalTcpBytesReceived(BatteryStats.STATS_CURRENT);
	long length1_2 = impl
			.getTotalTcpBytesReceived(BatteryStats.STATS_LAST);
	long length1_3 = impl
			.getTotalTcpBytesReceived(BatteryStats.STATS_TOTAL);
	long length1_4 = impl
			.getTotalTcpBytesReceived(BatteryStats.STATS_UNPLUGGED);
	long length2_1 = impl
			.getTotalTcpBytesSent(BatteryStats.STATS_CURRENT);
	long length2_2 = impl.getTotalTcpBytesSent(BatteryStats.STATS_LAST);
	long length2_3 = impl
			.getTotalTcpBytesSent(BatteryStats.STATS_TOTAL);
	long length2_4 = impl
			.getTotalTcpBytesSent(BatteryStats.STATS_UNPLUGGED);
	long length3_1 = impl
			.getMobileTcpBytesReceived(BatteryStats.STATS_CURRENT);
	long length3_2 = impl
			.getMobileTcpBytesReceived(BatteryStats.STATS_LAST);
	long length3_3 = impl
			.getMobileTcpBytesReceived(BatteryStats.STATS_TOTAL);
	long length3_4 = impl
			.getMobileTcpBytesReceived(BatteryStats.STATS_UNPLUGGED);
	long length4_1 = impl
			.getMobileTcpBytesSent(BatteryStats.STATS_CURRENT);
	long length4_2 = impl
			.getMobileTcpBytesSent(BatteryStats.STATS_LAST);
	long length4_3 = impl
			.getMobileTcpBytesSent(BatteryStats.STATS_TOTAL);
	long length4_4 = impl
			.getMobileTcpBytesSent(BatteryStats.STATS_UNPLUGGED);
	Log.d("TAG", "total tcp R dataC:" + length1_1 / (1024 * 1024));
	Log.d("TAG", "total tcp R dataL:" + length1_2 / (1024 * 1024));
	Log.d("TAG", "total tcp R dataT:" + length1_3 / (1024 * 1024));
	Log.d("TAG", "total tcp R dataU:" + length1_4 / (1024 * 1024));
	Log.d("TAG", "total tcp S dataC:" + length2_1 / (1024 * 1024));
	Log.d("TAG", "total tcp S dataL:" + length2_2 / (1024 * 1024));
	Log.d("TAG", "total tcp S dataT:" + length2_3 / (1024 * 1024));
	Log.d("TAG", "total tcp S dataU:" + length2_4 / (1024 * 1024));
	Log.d("TAG", "M R tcp dataC:" + length3_1 / (1024 * 1024));
	Log.d("TAG", "M R tcp dataL:" + length3_2 / (1024 * 1024));
	Log.d("TAG", "M R tcp dataT:" + length3_3 / (1024 * 1024));
	Log.d("TAG", "M R tcp dataU:" + length3_4 / (1024 * 1024));
	Log.d("TAG", "M S tcp dataC:" + length4_1 / (1024 * 1024));
	Log.d("TAG", "M S tcp dataL:" + length4_2 / (1024 * 1024));
	Log.d("TAG", "M S tcp dataT:" + length4_3 / (1024 * 1024));
	Log.d("TAG", "M S tcp dataU:" + length4_4 / (1024 * 1024));
} catch (RemoteException e) {
	e.printStackTrace();
}
```