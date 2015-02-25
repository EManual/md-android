本例参考ApiDemos中NFC的ForegoundDispatch来介绍编写Android NFC的基本步骤，因为手边只有MifareClassic类型的Tag，需要对ForegoundDispatch的代码做些修改来检测MifareClassic 的类型的NFC Tag，读写其他类型的NFC Tag的基本步骤是一致的。
1. 在Android manifest 文件中申明和NFC相关的权限和功能选项：
权限申明：
```  
<uses-permission android:name="android.permission.NFC"/>
```
最低版本要求，NFC是指Android2.3 (Level 10) 才开始支持的，因此最低版本要求必须指定为10。
```  
<uses-sdk android:minSdkVersion="10"/>
```
如果需要在Android Market上发布，需要指定手机支持NFC功能。
```  
<uses-feature android:name="android.hardware.nfc" android:required="true" />
```
为Activity申明它支持处理NFC Tag
比如我们的示例Activity 在Manifest 的申明如下：
```  
<activity
    android:name=".NFCDemoActivity"
    android:label="@string/app_name"
    android:launchMode="singleTop" >
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    <intent-filter>
        <action android:name="android.nfc.action.NDEF_DISCOVERED" />
        <data android:mimeType="text/plain" />
    </intent-filter>
    <intent-filter>
        <action android:name="android.nfc.action.TAG_DISCOVERED" >
        </action>
        <category android:name="android.intent.category.DEFAULT" >
        </category>
    </intent-filter>
    <intent-filter>
        <action android:name="android.nfc.action.TECH_DISCOVERED" />
    </intent-filter>
    <meta-data
        android:name="android.nfc.action.TECH_DISCOVERED"
        android:resource="@xml/filter_nfc" />
</activity>
```
三种Activity NDEF_DISCOVERED，TECH_DISCOVERED,TAG_DISCOVERED指明的先后顺序非常重要，当Android设备检测到有NFC Tag靠近时，会根据Action申明的顺序给对应的Activity发送含NFC消息的Intent。
2. Android NFC 消息发送机制
当Android设备检测到有NFC Tag时，理想的行为是触发最合适的Activity来处理检测到的Tag，这是因为NFC通常是在非常近的距离才起作用(<4m)，如果此时需要用户来选择合适的应用来处理Tag，很容易断开与Tag之间的通信。因此你需要选择合适的Intent filter 只处理你想读写的Tag类型。
Android系统支持两种NFC消息发送机制：Intent 发送机制和前台Activity 消息发送机制。
Intent 发送机制 当系统检测到Tag时，Android系统提供manifest 中定义的Intent filter来选择合适的Activity来处理对应的Tag，当有多个Activity可以处理对应的Tag类型时，则会显示Activity选择窗口由用户选择：
![img](P)  
前台Activity 消息发送机制 允许一个在前台运行的Activity在读写NFC Tag具有优先权，此时如果Android检测到有NFC Tag，如果前台允许的Activity可以处理该种类型的Tag则该Activity具有优先权，而不出现Activity 选择窗口。
这两种方法基本上都是使用Intent-filter 来指明Activity可以处理的Tag类型，一个是使用Android的Manifest 来说明，一个是通过代码来申明。
下图显示当Android检测到Tag，消息发送的优先级：
![img](P)  
本例 NFCDemoActivity 支持两种NFC消息发送机制，上面的XML指明了Intent 消息发送机制，其中
```  
<meta-data android:name="android.nfc.action.TECH_DISCOVERED"
	android:resource="@xml/filter_nfc"
/>
```
的filter_nfc 指明了支持处理的NFC Tag类型，filter_nfc.xml 定义如下：
```  
<resources xmlns:xliff="urn:oasis:names:tc:xliff:document:1.2">
	<!?C capture anything using NfcF ?C>
	<tech-list>
		<tech>android.nfc.tech.NfcA</tech>
		<tech>android.nfc.tech.MifareClassic</tech>
		<tech>android.nfc.tech.MifareUltralight</tech>
	</tech-list>
</resources>
```
因为我只有MifareClassic 类型的Tag，所以只定义了MifareClassic相关的Tag类型，如果你可以处理所有Android支持的NFC类型，可以定义为：
```  
<resources xmlns:xliff="urn:oasis:names:tc:xliff:document:1.2">
	<tech-list>
		<tech>android.nfc.tech.IsoDep</tech>
		<tech>android.nfc.tech.NfcA</tech>
		<tech>android.nfc.tech.NfcB</tech>
		<tech>android.nfc.tech.NfcF</tech>
		<tech>android.nfc.tech.NfcV</tech>
		<tech>android.nfc.tech.Ndef</tech>
		<tech>android.nfc.tech.NdefFormatable</tech>
		<tech>android.nfc.tech.MifareClassic</tech>
		<tech>android.nfc.tech.MifareUltralight</tech>
	</tech-list>
</resources>
```
有了这个Manifest中的申明，当Android检测到有Tag时，会显示Activity选择窗口，如上图中的Reading Example。
当NFCDemoActiviy在前台运行时，我们希望只有它来处理Mifare 类型的Tag，此时可以使用前台消息发送机制，下面的代码基本和ApiDemos中的NFC示例类似：
```  
public class NFCDemoActivity extends Activity {
	private NfcAdapter mAdapter;
	private PendingIntent mPendingIntent;
	private IntentFilter[] mFilters;
	private String[][] mTechLists;
	private TextView mText;
	private int mCount = 0;
	@Override
	public void onCreate(Bundle savedState) {
		super.onCreate(savedState);
		setContentView(R.layout.foreground_dispatch);
		mText = (TextView) findViewById(R.id.text);
		mText.setText("Scan a tag");
		mAdapter = NfcAdapter.getDefaultAdapter(this);
		// Create a generic PendingIntent that will be deliver
		// to this activity. The NFC stack
		// will fill in the intent with the details of the
		// discovered tag before delivering to
		// this activity.
		mPendingIntent = PendingIntent.getActivity(this, 0, new Intent(this,
				getClass()).addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP), 0);
		// Setup an intent filter for all MIME based dispatches
		IntentFilter ndef = new IntentFilter(NfcAdapter.ACTION_TECH_DISCOVERED);
		try {
			ndef.addDataType("*/*");
		} catch (MalformedMimeTypeException e) {
			throw new RuntimeException("fail", e);
		}
		mFilters = new IntentFilter[] { ndef, };
		// Setup a tech list for all MifareClassic tags
		mTechLists = new String[][] { new String[] { MifareClassic.class
				.getName() } };
	}
	@Override
	public void onResume() {
		super.onResume();
		mAdapter.enableForegroundDispatch(this, mPendingIntent, mFilters,
				mTechLists);
	}
	@Override
	public void onNewIntent(Intent intent) {
		Log.i("Foreground dispatch", "Discovered tag with intent: " + intent);
		mText.setText("Discovered tag " + ++mCount + " with intent: " + intent);
	}
	@Override
	public void onPause() {
		super.onPause();
		mAdapter.disableForegroundDispatch(this);
	}
}
```
只改了一行，将处理NfcF类型的Tag 改为处理MifareClassic 类型的NFC Tag。
```  
mTechLists = new String[][] { new String[] { MifareClassic.class.getName() } };
```
运行该示例，每靠近一次Tag，计数加1。
![img](P)  