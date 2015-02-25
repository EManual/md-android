在Android4.0推出的时候，一个非常引人注目的功能就是NFC(Near Field Communication).
Near Field Communication (NFC) is a set of short-range wireless technologies, typically requiring a distance of 4cm or less to initiate a connection. NFC allows you to share small payloads of data between an NFC tag and an Android-powered device, or between two Android-powered devices.
翻译：
近场通讯(NFC)是一系列短距离无线技术，一般需要4cm或者更短去初始化连接。近场通讯(NFC)允许你在NFC tag和Android设备或者两个Android设备间共享小负载数据。
典型的应用为刷卡应用，如刷信用卡，公交车卡，吃饭的饭卡之类。腾讯2011年1月份文章“Android首款NFC近场通信应用推出”，说明了基于Android的NFC应用目前已经有了，得益于日本在手机刷卡的应用氛围。据2011年7月网易文章“PayPal推出Android系统NFC移动支付服务”报道，PayPal已经做了尝试了，相信这股风很快要刮到中国。
下面我们从技术的层面来分析一下这个技术。
这是NFC的开发流程，参考文章 “【NFC在android中的应用API】”。
相关的类代码有：NfcAdapter,NdefMessage, NdefRecord,ACTION_TAG_DISCOVERED.
在功能层面上，涉及到了NFC的读写功能。下面我们分别来做总结。
在代码层上面：
使用的时候，需要在AndroidManifest.xml里面加一些权限以及属性。
```  
<uses-permission android:name="android.permission.NFC"/>
<uses-feature android:name="android.hardware.nfc" android:required="true"/>
<uses-sdk android:minSdkVersion="10"/>
```
这里注意，在Android Version 9的时候仅仅支持了ACTION_TAG_DISCOVERED，对于其他的需要10以上。
在上面的基础上，还需要增加intent-filter的支持。
```  
<intent-filter> 
	<action android:name="android.nfc.action.NDEF_DISCOVERED"/>
	<category android:name="android.intent.category.DEFAULT"/>
	<data android:mimeType="text/plain"/>
</intent-filter>
```
获取NfcAdapter的代码为
```  
public static String getStatusNfcDevice() {
	NfcAdapter nfcAdapter = NfcAdapter.getDefaultAdapter();
	if (nfcAdapter.isEnabled()) {
		String status = "enabled";
		return status;
	} else {
		String status = "disabled";
		return status;
	}
}
```
处理函数为
```  
public void onResume() {
	super.onResume();
	if (NfcAdapter.ACTION_NDEF_DISCOVERED.equals(getIntent().getAction())) {
		Parcelable[] rawMsgs = intent
				.getParcelableArrayExtra(NfcAdapter.EXTRA_NDEF_MESSAGES);
		if (rawMsgs != null) {
			msgs = new NdefMessage[rawMsgs.length];
			for (int i = 0; i < rawMsgs.length; i++) {
				msgs[i] = (NdefMessage) rawMsgs[i];
			}
		}
	}
	// process the msgs array
}
```
完整的一个操作代码摘自Google Android NFC Guide代码（略加注释）：
```  
import android.app.Activity;
import android.content.Intent;
import android.nfc.NdefMessage;
import android.nfc.NdefRecord;
import android.nfc.NfcAdapter;
import android.nfc.NfcAdapter.CreateNdefMessageCallback;
import android.nfc.NfcEvent;
import android.os.Bundle;
import android.os.Parcelable;
import android.widget.TextView;
import android.widget.Toast;
import java.nio.charset.Charset;

//继承并实现接口CreateNdefMessageCallback方法createNdefMessage
public class Beam extends Activity implements CreateNdefMessageCallback {
	NfcAdapter mNfcAdapter;
	TextView textView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		TextView textView = (TextView) findViewById(R.id.textView);
		// Check for available NFC Adapter
		// 检测是否有NFC适配器
		mNfcAdapter = NfcAdapter.getDefaultAdapter(this);
		if (mNfcAdapter == null) {
			Toast.makeText(this, "NFC is not available", Toast.LENGTH_LONG)
					.show();
			finish();
			return;
		}
		// Register callback
		// 注册回调函数
		mNfcAdapter.setNdefPushMessageCallback(this, this);
	}

	@Override
	public NdefMessage createNdefMessage(NfcEvent event) {
		String text = ("Beam me up, Android!\n\n" + "Beam Time: " + System
				.currentTimeMillis());
		// 回调函数，构造NdefMessage格式
		NdefMessage msg = new NdefMessage(new NdefRecord[] { createMimeRecord(
				"application/com.example.android.beam", text.getBytes())
		[Tags]/**
		 [Tags]* The Android Application Record (AAR) is commented out. When a device
		 [Tags]* receives a push with an AAR in it, the application specified in the
		 [Tags]* AAR is guaranteed to run. The AAR overrides the tag dispatch system.
		 [Tags]* You can add it back in to guarantee that this activity starts when
		 [Tags]* receiving a beamed message. For now, this code uses the tag dispatch
		 [Tags]* system.
		 [Tags]*/
		// ,NdefRecord.createApplicationRecord("com.example.android.beam")
				});
		return msg;
	}
	@Override
	public void onResume() {
		super.onResume();
		// Check to see that the Activity started due to an Android Beam
		// 得到是否检测到ACTION_NDEF_DISCOVERED触发
		if (NfcAdapter.ACTION_NDEF_DISCOVERED.equals(getIntent().getAction())) {
			processIntent(getIntent());
		}
	}
	// 重载Activity类方法处理当新Intent到来事件
	@Override
	public void onNewIntent(Intent intent) {
		// onResume gets called after this to handle the intent
		setIntent(intent);
	}
	[Tags]/**
	 [Tags]* Parses the NDEF Message from the intent and prints to the TextView
	 [Tags]*/
	// 关键处理函数，处理扫描到的NdefMessage
	void processIntent(Intent intent) {
		textView = (TextView) findViewById(R.id.textView);
		Parcelable[] rawMsgs = intent
				.getParcelableArrayExtra(NfcAdapter.EXTRA_NDEF_MESSAGES);
		// only one message sent during the beam
		NdefMessage msg = (NdefMessage) rawMsgs[0];
		// record 0 contains the MIME type, record 1 is the AAR, if present
		textView.setText(new String(msg.getRecords()[0].getPayload()));
	}
	[Tags]/**
	 [Tags]* Creates a custom MIME type encapsulated in an NDEF record
	 [Tags]*/
	public NdefRecord createMimeRecord(String mimeType, byte[] payload) {
		byte[] mimeBytes = mimeType.getBytes(Charset.forName("US-ASCII"));
		NdefRecord mimeRecord = new NdefRecord(NdefRecord.TNF_MIME_MEDIA,
				mimeBytes, new byte[0], payload);
		return mimeRecord;
	}
}
```
上面代码还需要在AndroidManifest.xml文件里面添加
```  
<intent-filter>
  <action android:name="android.nfc.action.NDEF_DISCOVERED"/>
  <category android:name="android.intent.category.DEFAULT"/>
  <data android:mimeType="application/com.example.android.beam"/>
</intent-filter>
```
在对NFC设备进行写操作的时候，相关代码:
```  
public class Snippet {
	private void enableTagWriteMode() {
		mWriteMode = true;
		IntentFilter tagDetected = new IntentFilter(
				NfcAdapter.ACTION_TAG_DISCOVERED);
		mWriteTagFilters = new IntentFilter[] { tagDetected };
		mNfcAdapter.enableForegroundDispatch(this, mNfcPendingIntent,
				mWriteTagFilters, null);
	}
	@Override
	protected void onNewIntent(Intent intent) {
		// Tag writing mode
		if (mWriteMode
				&& NfcAdapter.ACTION_TAG_DISCOVERED.equals(intent.getAction())) {
			Tag detectedTag = intent.getParcelableExtra(NfcAdapter.EXTRA_TAG);
			if (NfcUtils.writeTag(NfcUtils.getPlaceidAsNdef(placeidToWrite),
					detectedTag)) {
				Toast.makeText(this, "Success: Wrote placeid to nfc tag",
						Toast.LENGTH_LONG).show();
				NfcUtils.soundNotify(this);
			} else {
				Toast.makeText(this, "Write failed", Toast.LENGTH_LONG).show();
			}
		}
	}
	/*
	 [Tags]* Writes an NdefMessage to a NFC tag
	 [Tags]*/
	public static boolean writeTag(NdefMessage message, Tag tag) {
		int size = message.toByteArray().length;
		try {
			Ndef ndef = Ndef.get(tag);
			if (ndef != null) {
				ndef.connect();
				if (!ndef.isWritable()) {
					return false;
				}
				if (ndef.getMaxSize() < size) {
					return false;
				}
				ndef.writeNdefMessage(message);
				return true;
			} else {
				NdefFormatable format = NdefFormatable.get(tag);
				if (format != null) {
					try {
						format.connect();
						format.format(message);
						return true;
					} catch (IOException e) {
						return false;
					}
				} else {
					return false;
				}
			}
		} catch (Exception e) {
			return false;
		}
	}
}
<activity android:name=".CheckInActivity">
	<intent-filter>
		<action android:name="android.nfc.action.NDEF_DISCOVERED"/>
		<data android:mimeType="application/vnd.facebook.places"/>
		<category android:name="android.intent.category.DEFAULT"/>
	</intent-filter>
</activity>
```