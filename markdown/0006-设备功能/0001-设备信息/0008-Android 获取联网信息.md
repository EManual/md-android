与网络相关的，就经常要做网络状态判断及信息获取。
用到的类ConnectivityManager 和NetworkInfo 
```  
//获取网络连接管理者
ConnectivityManager connectionManager = (ConnectivityManager)      
           context.getSystemService(Context.CONNECTIVITY_SERVICE);    
//获取网络的状态信息，有下面三种方式
NetworkInfo networkInfo = connectionManager.getActiveNetworkInfo();
getDetailedState()：获取详细状态。
getExtraInfo()：获取附加信息。
getReason()：获取连接失败的原因。
getType()：获取网络类型(一般为移动或Wi-Fi)。
getTypeName()：获取网络类型名称(一般取值“WIFI”或“MOBILE”)。
isAvailable()：判断该网络是否可用。
isConnected()：判断是否已经连接。
isConnectedOrConnecting()：判断是否已经连接或正在连接。
isFailover()：判断是否连接失败。
isRoaming()：判断是否漫游

当用wifi上的时候，getType是WIFI，getExtraInfo是空的。
当用手机上的时候，getType是MOBILE。
用移动CMNET方式，getExtraInfo的值是cmnet。
用移动CMWAP方式，getExtraInfo的值是cmwap，但是不在代理的情况下访问普通的网站访问不了。
用联通3gwap方式，getExtraInfo的值是3gwap。
用联通3gnet方式，getExtraInfo的值是3gnet。
用联通uniwap方式，getExtraInfo的值是uniwap。
用联通uninet方式，getExtraInfo的值是uninet。
```
尝试以下方法获取networktype
```  
TelephonyManager telephonyManager =  
    (TelephonyManager)context.getSystemService(Context.TELEPHONY_SERVICE);
int networkType = telephonyManager.getNetworkType();
Log.e("network Type", networkType + "");
Log.e("network CountryIso", telephonyManager.getNetworkCountryIso());
Log.e("network Operator", telephonyManager.getNetworkOperator());
Log.e("network OperatorName", telephonyManager.getNetworkOperatorName());
```
其中：
```  
[Tags]/** Network type is unknown */
 //public static final int NETWORK_TYPE_UNKNOWN = 0;
[Tags]/** Current network is GPRS */
//public static final int NETWORK_TYPE_GPRS = 1;
[Tags]/** Current network is EDGE */
//public static final int NETWORK_TYPE_EDGE = 2;
[Tags]/** Current network is UMTS */
//public static final int NETWORK_TYPE_UMTS = 3;
[Tags]/** Current network is CDMA: Either IS95A or IS95B*/
//public static final int NETWORK_TYPE_CDMA = 4;
[Tags]/** Current network is EVDO revision 0*/
//public static final int NETWORK_TYPE_EVDO_0 = 5;
[Tags]/** Current network is EVDO revision A*/
//public static final int NETWORK_TYPE_EVDO_A = 6;
[Tags]/** Current network is 1xRTT*/
//public static final int NETWORK_TYPE_1xRTT = 7;
[Tags]/** Current network is HSDPA */
//public static final int NETWORK_TYPE_HSDPA = 8;
[Tags]/** Current network is HSUPA */
//public static final int NETWORK_TYPE_HSUPA = 9;
[Tags]/** Current network is HSPA */
//public static final int NETWORK_TYPE_HSPA = 10;
[Tags]/** Current network is iDen */
//public static final int NETWORK_TYPE_IDEN = 11;
```
下面总结一些常用方法：
```  
import java.net.InetAddress;
import java.net.NetworkInterface;
import java.net.SocketException;
import java.util.Enumeration;
import android.content.Context;
import android.net.ConnectivityManager;
import android.net.NetworkInfo;
import android.net.wifi.WifiInfo;
import android.net.wifi.WifiManager;
import android.telephony.TelephonyManager;
import android.util.Log;
[Tags]/**
 [Tags]* 网络信息
 [Tags]*/
public class NetworkUtil {
	private static final String TAG = "NetworkUtil";
	[Tags]/**
	 [Tags]* 判断网络是否可用 <br>
	 [Tags]* code from: http://www.androidsnippets.com/have-internet
	 [Tags]* @param context
	 [Tags]*/
	public static boolean haveInternet(Context context) {
		NetworkInfo info = (NetworkInfo) ((ConnectivityManager) context
				.getSystemService(Context.CONNECTIVITY_SERVICE))
				.getActiveNetworkInfo();

		if (info == null || !info.isConnected()) {
			return false;
		}
		if (info.isRoaming()) {
			// here is the roaming option you can change it if you want to
			// disable internet while roaming, just return false
			// 是否在漫游，可根据程序需求更改返回值
			return false;
		}
		return true;
	}
	[Tags]/**
	 [Tags]* 判断网络是否可用
	 [Tags]* 
	 [Tags]* @param context
	 [Tags]* @return
	 [Tags]*/
	public static boolean isnetWorkAvilable(Context context) {
		ConnectivityManager connectivityManager = (ConnectivityManager) context
				.getSystemService(Context.CONNECTIVITY_SERVICE);
		if (connectivityManager == null) {
			Log.e(TAG, "couldn't get connectivity manager");
		} else {
			NetworkInfo[] networkInfos = connectivityManager
					.getAllNetworkInfo();
			if (networkInfos != null) {
				for (int i = 0, count = networkInfos.length; i < count; i++) {
					if (networkInfos[i].getState() == NetworkInfo.State.CONNECTED) {
						return true;
					}
				}
			}
		}
		return false;
	}
	[Tags]/**
	 [Tags]* IP地址<br>
	 [Tags]* code from:
	 [Tags]* http://www.droidnova.com/get-the-ip-address-of-your-device,304.html <br>
	 [Tags]* 
	 [Tags]* @return 如果返回null，证明没有网络链接。 如果返回String，就是设备当前使用的IP地址，不管是WiFi还是3G
	 [Tags]*/
	public static String getLocalIpAddress() {
		try {
			for (Enumeration<NetworkInterface> en = NetworkInterface
					.getNetworkInterfaces(); en.hasMoreElements();) {
				NetworkInterface intf = en.nextElement();
				for (Enumeration<InetAddress> enumIpAddr = intf
						.getInetAddresses(); enumIpAddr.hasMoreElements();) {
					InetAddress inetAddress = enumIpAddr.nextElement();
					if (!inetAddress.isLoopbackAddress()) {
						return inetAddress.getHostAddress().toString();
					}
				}
			}
		} catch (SocketException ex) {
			Log.e("getLocalIpAddress", ex.toString());
		}
		return null;
	}
	[Tags]/**
	 [Tags]* 获取MAC地址 <br>
	 [Tags]* code from: http://orgcent.com/android-wifi-mac-ip-address/
	 [Tags]* 
	 [Tags]* @param context
	 [Tags]* @return
	 [Tags]*/
	public static String getLocalMacAddress(Context context) {
		WifiManager wifi = (WifiManager) context
				.getSystemService(Context.WIFI_SERVICE);
		WifiInfo info = wifi.getConnectionInfo();
		return info.getMacAddress();
	}
	[Tags]/**
	 [Tags]* WIFI 是否可用
	 [Tags]* 
	 [Tags]* @param context
	 [Tags]* @return
	 [Tags]*/
	public static boolean isWiFiActive(Context context) {
		ConnectivityManager connectivity = (ConnectivityManager) context
				.getSystemService(Context.CONNECTIVITY_SERVICE);
		if (connectivity != null) {
			NetworkInfo[] info = connectivity.getAllNetworkInfo();
			if (info != null) {
				for (int i = 0; i < info.length; i++) {
					if (info[i].getTypeName().equals("WIFI")
							&& info[i].isConnected()) {
						return true;
					}
				}
			}
		}
		return false;
	}
	[Tags]/**
	 [Tags]* 存在多个连接点
	 [Tags]* 
	 [Tags]* @param context
	 [Tags]* @return
	 [Tags]*/
	public static boolean hasMoreThanOneConnection(Context context) {
		ConnectivityManager manager = (ConnectivityManager) context
				.getSystemService(Context.CONNECTIVITY_SERVICE);
		if (manager == null) {
			return false;
		} else {
			NetworkInfo[] info = manager.getAllNetworkInfo();
			int counter = 0;
			for (int i = 0; i < info.length; i++) {
				if (info[i].isConnected()) {
					counter++;
				}
			}
			if (counter > 1) {
				return true;
			}
		}
		return false;
	}
	/*
	 [Tags]* HACKISH: These constants aren't yet available in my API level (7), but I
	 [Tags]* need to handle these cases if they come up, on newer versions
	 [Tags]*/
	public static final int NETWORK_TYPE_EHRPD = 14; // Level 11
	public static final int NETWORK_TYPE_EVDO_B = 12; // Level 9
	public static final int NETWORK_TYPE_HSPAP = 15; // Level 13
	public static final int NETWORK_TYPE_IDEN = 11; // Level 8
	public static final int NETWORK_TYPE_LTE = 13; // Level 11
	[Tags]/**
	 [Tags]* Check if there is any connectivity
	 [Tags]* 
	 [Tags]* @param context
	 [Tags]* @return
	 [Tags]*/
	public static boolean isConnected(Context context) {
		ConnectivityManager cm = (ConnectivityManager) context
				.getSystemService(Context.CONNECTIVITY_SERVICE);
		NetworkInfo info = cm.getActiveNetworkInfo();
		return (info != null && info.isConnected());
	}
	[Tags]/**
	 [Tags]* Check if there is fast connectivity
	 [Tags]* 
	 [Tags]* @param context
	 [Tags]* @return
	 [Tags]*/
	public static boolean isConnectedFast(Context context) {
		ConnectivityManager cm = (ConnectivityManager) context
				.getSystemService(Context.CONNECTIVITY_SERVICE);
		NetworkInfo info = cm.getActiveNetworkInfo();
		return (info != null && info.isConnected() && isConnectionFast(
				info.getType(), info.getSubtype()));
	}
	[Tags]/**
	 [Tags]* Check if the connection is fast
	 [Tags]* 
	 [Tags]* @param type
	 [Tags]* @param subType
	 [Tags]* @return
	 [Tags]*/
	public static boolean isConnectionFast(int type, int subType) {
		if (type == ConnectivityManager.TYPE_WIFI) {
			System.out.println("CONNECTED VIA WIFI");
			return true;
		} else if (type == ConnectivityManager.TYPE_MOBILE) {
			switch (subType) {
			case TelephonyManager.NETWORK_TYPE_1xRTT:
				return false; // ~ 50-100 kbps
			case TelephonyManager.NETWORK_TYPE_CDMA:
				return false; // ~ 14-64 kbps
			case TelephonyManager.NETWORK_TYPE_EDGE:
				return false; // ~ 50-100 kbps
			case TelephonyManager.NETWORK_TYPE_EVDO_0:
				return true; // ~ 400-1000 kbps
			case TelephonyManager.NETWORK_TYPE_EVDO_A:
				return true; // ~ 600-1400 kbps
			case TelephonyManager.NETWORK_TYPE_GPRS:
				return false; // ~ 100 kbps
			case TelephonyManager.NETWORK_TYPE_HSDPA:
				return true; // ~ 2-14 Mbps
			case TelephonyManager.NETWORK_TYPE_HSPA:
				return true; // ~ 700-1700 kbps
			case TelephonyManager.NETWORK_TYPE_HSUPA:
				return true; // ~ 1-23 Mbps
			case TelephonyManager.NETWORK_TYPE_UMTS:
				return true; // ~ 400-7000 kbps
								// NOT AVAILABLE YET IN API LEVEL 7
			case NETWORK_TYPE_EHRPD:
				return true; // ~ 1-2 Mbps
			case NETWORK_TYPE_EVDO_B:
				return true; // ~ 5 Mbps
			case NETWORK_TYPE_HSPAP:
				return true; // ~ 10-20 Mbps
			case NETWORK_TYPE_IDEN:
				return false; // ~25 kbps
			case NETWORK_TYPE_LTE:
				return true; // ~ 10+ Mbps
								// Unknown
			case TelephonyManager.NETWORK_TYPE_UNKNOWN:
				return false;
			default:
				return false;
			}
		} else {
			return false;
		}
	}
	[Tags]/**
	 [Tags]* IP转整型
	 [Tags]* 
	 [Tags]* @param ip
	 [Tags]* @return
	 [Tags]*/
	public static long ip2int(String ip) {
		String[] items = ip.split("\\.");
		return Long.valueOf(items[0]) << 24 | Long.valueOf(items[1]) << 16
				| Long.valueOf(items[2]) << 8 | Long.valueOf(items[3]);
	}
	[Tags]/**
	 [Tags]* 整型转IP
	 [Tags]* 
	 [Tags]* @param ipInt
	 [Tags]* @return
	 [Tags]*/
	public static String int2ip(long ipInt) {
		StringBuilder sb = new StringBuilder();
		sb.append(ipInt & 0xFF).append(".");
		sb.append((ipInt >> 8) & 0xFF).append(".");
		sb.append((ipInt >> 16) & 0xFF).append(".");
		sb.append((ipInt >> 24) & 0xFF);
		return sb.toString();
	}
}
```
PS： WIFI网络信息获取，以后再补充。