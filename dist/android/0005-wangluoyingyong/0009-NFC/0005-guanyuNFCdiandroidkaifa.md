今日，姜饼系统对NFC技术进行了在技术上的支持和扩展，改进后的新平台将会给程序提供一套对API的标准：
```  
NFC-A(ISO 14443-3A)
NFC-B(ISO 14443-3B)
NFC-F(JIS 6319-4)
NFC-V(ISO 15693)
ISO-DEP(ISO 14443-4)
Mifare classic
Mifare Ultralight
NFC Forum NDEF tags
```
此平台提供了对等的通信协议和API，Activity可以使用API来注册NDFF信息，在之前的版本中，在安卓软件开发的整个平台是使用的单步INTENT来传递tag通知的，更新的版本为四个版本确保一个tag被传递，新版的发送过程中同样需要进tag内容和tag语法的应用监听。
其中api提供了两个包，分别是android.nfc和 android.nfc.tech，两个包中有几个关键类需要我们知道，下面我就给大家来介绍一下两个包中的关键类。
NfcAdapter类：此类是设备对NFC硬件的描述。
NdefMessage类：此类对数据消息设备消息还有tag传输的标准形式进行了描述。
NdefRecord类： NdefRecord类包扩在了NdefMessage类中,它主要记录的是发送传递的原始数据。
Tag类： 此类描述了设备上的标签.不同的标签支持依赖于 tag TagTechnology。
TagTechnology类：整合了支持读写访问标签技术所需要的标签属性和I/O操作。
因为NFC依赖于无线技术，所以2.3平台中的NFC API是存在的，如果我们需要判断设备是否支持NFC，我们可以通过getDefaultAdapter(Context)和context.getPackageManager().hasSystemFeature(PackageManager.FEATURE_NFC)的返回值来判断，如果是false表示不支持。