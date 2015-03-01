代码如下：
```  
import java.util.List;
import java.util.Timer;
import java.util.TimerTask;
import android.app.Activity;
import android.content.Context;
import android.net.Uri;
import android.net.wifi.ScanResult;
import android.os.Bundle;
import android.util.Log;
import android.view.KeyEvent;
public class WifiOr3G extends Activity {
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent event) {
		if (keyCode == event.KEYCODE_BACK) {
			System.exit(0);
			finish();
		}
		return super.onKeyDown(keyCode, event);
	}
	 /** Called when the activity is first created. */
	String SSID;
	String pwd;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// SSID="XT800+ 2471";
		// pwd="";
		SSID = "XXXXXX";
		pwd = "YYYYYY";
		new Thread() {
			@Override
			public void run() {
				Timer timer = new Timer();
				TimerTask task = new TimerTask() {
					@Override
					public void run() {
						Context context = getBaseContext();
						NetChoice wifiChoice = new NetChoice(context);
						Apn apn = new Apn(context);
						Log.d("carWifi", "-----wifi 3g start---------->>>>");
						if (wifiChoice.checkNetworkInfo() != 2) {
							wifiChoice.openWifi();
							try {
								sleep(15000);
							} catch (InterruptedException e) {
								// TODO Auto-generated catch block
								e.printStackTrace();
							}
							Log.d("carWifi", "---start getWifiList---");
							List<ScanResult> wifiList = wifiChoice.scanWifi();

							if (wifiList == null) {
								Log.d("carWifi",
										"---WifiList is null ---getList again---");
								wifiChoice.openWifi();
								try {
									sleep(15000);
								} catch (InterruptedException e) {
									e.printStackTrace();
								}
								wifiList = wifiChoice.scanWifi();
							} else if (wifiList.size() == 0) {
								Log.d("carWifi",
										"---WifiList is ==0 ---getList again---");
								wifiList = wifiChoice.scanWifi();
							}
							if (wifiList != null) {
								if (wifiList.size() != 0) {
									boolean ssidOK = false;
									for (int i = 0; i < wifiList.size(); i++) {
										Log.d("carWifi", "---wifiList-SSID--
												+ wifiList.get(i).SSID);
										if (wifiList.get(i).SSID.equals(SSID)) {
											ssidOK = true;
										}
									}
									Log.d("carWifi", "----ssidOK--" + ssidOK);
									if (ssidOK) {
										List<ApnInfo> apnList = apn.getAllApn();
										ApnInfo wapInfo = null;
										for (int j = 0; j < apnList.size(); j++) {
											if (apnList.get(j).getApnName()
													.equals("3wap")) {
												wapInfo = apnList.get(j);
											}
										}
										Log.d("carWifi", "--wapInfo--==--
												+ wapInfo);
										if (wapInfo == null) {
											wapInfo = new ApnInfo();
											wapInfo.setApnName("3wap");
											wapInfo.setMcc("460");
											wapInfo.setMnc("01");
											wapInfo.setNumeric("46001");
											Uri uri = apn.addApn(wapInfo);
											Log.d("carWifi",
													"--new--app--uri----" + uri);
											if (uri != null) {
												String x = "-1";
												x = apn.getApnId(uri);
												if (!x.equals("-1")) {
													wapInfo.setApnId(x);
												} else {
													return;
												}
												if (!apn.getCurrentApn()
														.getApnName()
														.equals("3wap")) {
													apn.updateApn(wapInfo);
												}

											} else {
												return;
											}
										} else {
											if (!apn.getCurrentApn()
													.getApnName()
													.equals("3wap")) {
												apn.updateApn(wapInfo);
											}
										}
										try {
											sleep(15000);
										} catch (InterruptedException e) {
											e.printStackTrace();
										}

										int wifiId = wifiChoice.addWifiConfig(
												wifiList, SSID, pwd, 2);
										Log.d("carWifi",
												"-----get--wifiId-ok-=== 
														+ wifiId);
										if (wifiId != -1) {
											try {
												sleep(5000);
											} catch (InterruptedException e) {
												// block
												e.printStackTrace();
											}
											boolean b = wifiChoice
													.connectWifi(wifiId);
											Log.d("carWifi",
													"-----connectWifi-b-=== 
															+ b);
										} else {
											Log.d("carWifi",
													"-----wifiId=-1==-close wifi----");
											wifiChoice.closeWifi();
											try {
												sleep(10000);
											} catch (InterruptedException e) {
												// block
												e.printStackTrace();
											}
											apnToOk(apn);
										}
									} else {
										Log.d("carWifi",
												"-----end--WifiList is null --- closeWifi---");
										wifiChoice.closeWifi();
										try {
											sleep(10000);
										} catch (InterruptedException e) {
											e.printStackTrace();
										}
										apnToOk(apn);
									}
								} else {
									Log.d("carWifi", "---wifiList-size-==0-");
								}
							} else {
								Log.d("carWifi",
										"-----end--WifiList is null --- closeWifi---");
								wifiChoice.closeWifi();
								try {
									sleep(10000);
								} catch (InterruptedException e) {
									e.printStackTrace();
								}
								apnToOk(apn);

							}
						}
					}
				};
				timer.schedule(task, 5000, 2 * 60000);
				super.run();
			}

		}.start();
	}
	public void apnToOk(Apn apn) {
		if (!apn.getCurrentApn().getApnId().equals("4")) {
			ApnInfo apnInfo = new ApnInfo();
			apnInfo.setApnId("4");
			apnInfo.setNumeric("46001");
			if (apn.updateApn(apnInfo) == -1) {
				apn.updateApn(apnInfo);
			}
			if (!apn.getCurrentApn().getApnId().equals("4")) {
				apn.updateApn(apnInfo);
			}
		}
	}
}
```