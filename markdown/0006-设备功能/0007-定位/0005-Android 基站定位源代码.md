经过几天的调研以及测试，终于解决了联通2G、移动2G、电信3G的基站定位代码。团队里面只有这些机器的制式了。下面就由我来做一个详细的讲解吧。
#### 1，相关技术内容
Google Android Api里面的TelephonyManager的管理。
联通、移动、电信不同制式在获取基站位置的代码区别。
通过基站的基本信息，通过Google Gears获取对应的GPS经纬度。
通过Google Map API根据GPS经纬度获取当前位置。
#### 2，目前存在的几个问题
由于得到的GPS经纬度在Google Map上面显示需要偏移，这块暂时没有进行处理。
没有使用PhoneStateListener来对状态实时进行更新。
没有使用线程异步获取数据
没有使用服务的方式来实时获取数据
所以如果是商业使用的话，还需进一步修改。
#### 3，当然本部分代码已经移植到我们的家庭卫士的项目中了，2提到的问题全部解决了。
下面我针对第一部分的四大内容进行代码注解。
#### 1 Google Android Api里面的TelephonyManager的管理。
```   
TelephonyManager tm = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);
```
通过这个方式就可以得到TelephonyManager接口。这个接口的源代码可以通过设置在项目里面查看，这里不具体附上了。得到TelephonyManager后，由于针对不同的运营商，代码有所不同，所以需要判断getNetworkType()在源代码里面有如下的类型定义 
```    
[Tags]/** Network type is unknown */
public static final int NETWORK_TYPE_UNKNOWN = 0;
[Tags]/** Current network is GPRS */
public static final int NETWORK_TYPE_GPRS = 1;
[Tags]/** Current network is EDGE */
public static final int NETWORK_TYPE_EDGE = 2;
[Tags]/** Current network is UMTS */
public static final int NETWORK_TYPE_UMTS = 3;
[Tags]/** Current network is CDMA: Either IS95A or IS95B */
public static final int NETWORK_TYPE_CDMA = 4;
[Tags]/** Current network is EVDO revision 0 */
public static final int NETWORK_TYPE_EVDO_0 = 5;
[Tags]/** Current network is EVDO revision A */
public static final int NETWORK_TYPE_EVDO_A = 6;
[Tags]/** Current network is 1xRTT */
public static final int NETWORK_TYPE_1xRTT = 7;
[Tags]/** Current network is HSDPA */
public static final int NETWORK_TYPE_HSDPA = 8;
[Tags]/** Current network is HSUPA */
public static final int NETWORK_TYPE_HSUPA = 9;
[Tags]/** Current network is HSPA */
public static final int NETWORK_TYPE_HSPA = 10;
```
#### 2 联通、移动、电信不同制式在获取基站位置的代码区别。
这部分是我实际测试出来的，经过无数次的拆机，放卡，才实现了不同制式的完美实现。
代码如下：
```  
public static void main(String[] args) {
	TelephonyManager tm = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);
	int type = tm.getNetworkType();
	// 中国电信为CTC
	// NETWORK_TYPE_EVDO_A是中国电信3G的getNetworkType
	// NETWORK_TYPE_CDMA电信2G是CDMA
	if (type == TelephonyManager.NETWORK_TYPE_EVDO_A
			|| type == TelephonyManager.NETWORK_TYPE_CDMA
			|| type == TelephonyManager.NETWORK_TYPE_1xRTT) {
	}
	// 移动2G卡 + CMCC + 2
	// type = NETWORK_TYPE_EDGE
	else if (type == TelephonyManager.NETWORK_TYPE_EDGE) {
	}
	// 联通的2G经过测试 China Unicom 1 NETWORK_TYPE_GPRS
	else if (type == TelephonyManager.NETWORK_TYPE_GPRS) {
	} else {
		tv.setText("Current Not Support This Type.");
	}
}
```