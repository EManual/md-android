Android可以借助于gps实现定位，但是很多地方是使用gps无法定位比如在室内，而且gps定位的话速度慢。 
下面的代码实现了定位的大致功能
```  
 /*CellIDInfo.java 封装了cellid的信息 */
public class CellIDInfo {
public int cellId;
public String mobileCountryCode;
public String mobileNetworkCode;
public int locationAreaCode;
public String radioType;
public CellIDInfo(){}
}
/*WifiInfo.java 封装了wifi的信息 */
public class WifiInfo {
	public String mac;
	public WifiInfo() {
	}
}
/*CellIDInfoManager.java 可获取所有的CellIDInfo */
import java.util.ArrayList;
import java.util.List;
import android.content.Context;
import android.telephony.NeighboringCellInfo;
import android.telephony.PhoneStateListener;
import android.telephony.TelephonyManager;
import android.telephony.cdma.CdmaCellLocation;
import android.telephony.gsm.GsmCellLocation;
public class CellIDInfoManager {
	private TelephonyManager manager;
	private PhoneStateListener listener;
	private GsmCellLocation gsm;
	private CdmaCellLocation cdma;
	int lac;
	String current_ci, mcc, mnc;
	public CellIDInfoManager() {
	}
	public ArrayList<CellIDInfo> getCellIDInfo(Context context) {
		manager = (TelephonyManager) context
				.getSystemService(Context.TELEPHONY_SERVICE);
		listener = new PhoneStateListener();
		manager.listen(listener, 0);
		ArrayList<CellIDInfo> CellID = new ArrayList<CellIDInfo>();
		CellIDInfo currentCell = new CellIDInfo();
		int type = manager.getNetworkType();
		if (type == TelephonyManager.NETWORK_TYPE_GPRS
				|| type == TelephonyManager.NETWORK_TYPE_EDGE
				|| type == TelephonyManager.NETWORK_TYPE_HSDPA) {
			gsm = ((GsmCellLocation) manager.getCellLocation());
			if (gsm == null)
				return null;
			lac = gsm.getLac();
			mcc = manager.getNetworkOperator().substring(0, 3);
			mnc = manager.getNetworkOperator().substring(3, 5);
			currentCell.cellId = gsm.getCid();
			currentCell.mobileCountryCode = mcc;
			currentCell.mobileNetworkCode = mnc;
			currentCell.locationAreaCode = lac;
			currentCell.radioType = "gsm";
			CellID.add(currentCell);
			List<NeighboringCellInfo> list = manager.getNeighboringCellInfo();
			int size = list.size();
			for (int i = 0; i < size; i++) {
				CellIDInfo info = new CellIDInfo();
				info.cellId = list.get(i).getCid();
				info.mobileCountryCode = mcc;
				info.mobileCountryCode = mnc;
				info.locationAreaCode = lac;
				CellID.add(info);
			}
			return CellID;
		} else if (type == TelephonyManager.NETWORK_TYPE_CDMA
				|| type == TelephonyManager.NETWORK_TYPE_1xRTT) {

			cdma = ((CdmaCellLocation) manager.getCellLocation());
			if (cdma == null)
				return null;
			if ("460".equals(manager.getSimOperator().substring(0, 3)))
				return null;
		}
		return null;
	}
}
```