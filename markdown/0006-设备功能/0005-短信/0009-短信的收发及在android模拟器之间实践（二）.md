#### 引言
前面我们介绍都只是如何发送SMS消息，接下来我们介绍如何接收SMS消息，及另一种发短信的方式并增强为可以发生图片等，最后介绍一下emulator工具。本文的主要内容如下：
```  
1~5见Android 开发之旅：短信的收发及在android模拟器之间实践（一）
6、温故知新之Intent
7、准备工作：SmsMessage类
8、SMS接收程序
9、另一种发送短信的方式：使用Intent
10、增强SMS为MMS
```
#### 6、温故知新之Intent
此系列前面简单地接受过意图（Intent），这里再次简单介绍一下，在短信接收程序和使用Intent发送SMS中我们要用到。android应用程序的三大组件——Activities、Services、Broadcast Receiver，通过消息触发，这个消息就称作意图（Intent）。下面以Acitvity为例，介绍一下Intent。Android用Intent这个特殊的类实现在Activity与Activity之间的切换。Intent类用于描述应用的功能。在Intent的描述结构中，有两个最重要的部分：动作和动作对应的数据。典型的动作类型有MAIN、VIEW、PICK、EDIT等，我们在短信接收程序中就用到从广播意图中提取动作类型并判断是否是"android.provider.Telephony.SMS_RECEIVED"，进而作深一步的处理。而动作对应的数据则以URI的形式表示。例如，要查看一个人的联系方式，需要创建一个动作为VIEW的Intent，以及表示这个人的URI。
通过解析各种Intent，从一个屏幕导航到另一个屏幕是很简单的。当向前导航时，Activity将会调用startActivity("指定一个Intent")方法。然后，系统会在所有已安装的应用程序中定义的IntentFilter中查找，找到最匹配的Intent对应的Activity。新的Activity接收到指定的Intent的通知后，开始运行。当startActivity()方法被调用时，将触发解析指定Intent的动作，该机制提供了两个关键的好处：
1、Activity能够重复利用从其他组件中以Intent形式产生的请求。
2、Activity可以在任何时候被具有相同IntentFilter的新的Activity取代。
#### 7、准备工作：SmsMessage类
顾名思义，SmsMessage类是一个表示短信的类，为了更好地了解Android的短信机制及以后更好地编写短信相关程序，这里介绍一下该类的公有方法和常量，及嵌套枚举、类成员。
公有方法：
```  
public static int[] calculateLength (CharSequence msgBody, boolean use7bitOnly)
参数：
msgBody-要封装的消息、use7bitOnly-如果为TRUE，不是广播特定7-比特编码的部分字符被认为是单个空字符；如果为FALSE，且msgBody包含非7-比特可编码字符，长度计算使用16-比特编码。
返回值：
返回一个4个元素的int数组，int[0]表示要求使用的SMS数量、int[1]表示编码单元已使用的数量、int[2]表示剩余到下个消息的编码单元数量、int[3]表示编码单元大小的指示器。
public static int[] calculateLength(String  messageBody, boolean use7bitOnly)
参数和返回值跟上面类似
public static SmsMessage createFromPdu(byte[] pdu)
从原始的PDU（protocol description units）创建一个SmsMessage。这个方法很重要，在我们编写短信接收程序要用到，它从我们接收到的广播意图中获取的字节创建SmsMessage。
public String getDisplayMessageBody()
返回短信消息的主体，或者Email消息主体（如果这个消息来自一个Email网关）。如果消息主体不可用，返回null。这个方法也很重要，在我们编写短信接收程序也要用到。
public String getDisplayOriginatingAddress()
返回信息来源地址，或Email地址（如果消息来自Email网关）。如果消息主体不可用，返回null。这个方法在来电显示，短信接收程序中经常用到。
public String getEmailBody()
如果isEmail为TRUE，即是邮件，返回通过网关发送Email的地址，否则返回null。
public int getIndexOnIcc()
返回消息记录在ICC上的索引（从1开始的）
public String getMessageBody()
以一个String返回消息的主体，如果它存在且是基于文本的。
public SmsMessage.MessageClass getMessageClass()
返回消息的类。
public String getOriginatingAddress()
以String返回SMS信息的来电地址，或不可用时为null。
public byte[] getPdu()
返回消息的原始PDU数据。
public int getProtocolIdentifier()
获取协议标识符。
public String getPseudoSubject()
public String getServiceCenterAddress()
返回转播消息SMS服务中心的地址，如果没有的话为null。
public int getStatus()
GSM：为一个SMS-STATUS-REPORT消息，它返回状态报告的status字段。这个字段表示之前提交的SMS消息的状态。
CDMA：为不影响来自GSM的状态码，值移动到31-16比特。这个值由一个error类（25-16比特）和一个状态码（23-16比特）组成。
如果是0，表示之前发送的消息已经被收到。
public int getStatusOnIcc()
返回消息在ICC上的状态（已读、未读、已发送、未发送）。有下面的几个值：SmsManager.STATUS_ON_ICC_FREE、SmsManager.STATUS_ON_ICC_READ、SmsManager.STATUS_ON_ICC_UNREAD、SmsManager.STATUS_ON_ICC_SEND、SmsManager.STATUS_ON_ICC_UNSENT这几个值在上篇的SmsManager类介绍有讲到。
public static SmsMessage.SubmitPdu   getSubmitPdu  (
       String  scAddress, String  destinationAddress, 
short destinationPort, byte[] data, 
boolean statusReportRequested)
参数：scAddress - 服务中心的地址（Sercvice Centre address，为null即使用默认的）、destinationAddress - 消息的目的地址、destinationPort- 发送消息到目的的端口号、data - 消息数据。
返回值：一个包含编码了的SC地址（如果指定了的话）和消息内容的SubmitPdu，否则返回null，如果编码错误。
public static SmsMessage.SubmitPdu getSubmitPdu  (
       String  scAddress, String  destinationAddress,
       String  message, boolean statusReportRequested)
和上面类似。
public static int getTPLayerLengthForPDU(String  pdu)
返回指定SMS-SUBMIT PDU的TP-Layer-Length，长度单位是字节而不是十六进字符。
public long getTimestampMillis()
以currentTimeMillis()格式返回服务中心时间戳。
public byte[] getUserData()
返回用户数据减去用户数据头部（如果有的话）
public boolean isCphsMwiMessage()
判断是否是CPHS MWI消息
public boolean isEmail()
判断是否是Email，如果消息来自一个Email网关且Email发送者（sender）、主题（subject）、解析主体（parsed body）可用，则返回TRUE。
public boolean isMWIClearMessage()
判断消息是否是一个CPHS 语音邮件或消息等待MWI清除（clear）消息。
public boolean isMWISetMessage()
判断消息是否是一个CPHS 语音邮件或消息等待MWI设置（set）消息。
public boolean isMwiDontStore()
如果消息是一个“Message Waiting Indication Group：Discard Message”通知且不应该保存，则返回TRUE，否则返回FALSE。
public boolean isReplace()
判断是否是一个“replace short message”SMS
public boolean isReplyPathPresent()
判断消息的TP-Reply-Path位是否在消息中设置了。
public boolean isStatusReportMessage()
判断是否是一个SMS-STATUS-REPORT消息。
```
常量值：
```  
public static final int ENCODING_16BIT ：值为3(0x00000003)
public static final int ENCODING_8BIT ：值为2 (0x00000002)
public static final int ENCODING_UNKNOWN ：值为0 (0x00000000) ，用户数据编码单元的大小。
public static final int MAX_USER_DATA_BYTES ：值为140 (0x0000008c)，表示每个消息的最大负载字节数。
public static final int MAX_USER_DATA_BYTES_WITH_HEADER ：134 (0x00000086)，如果一个用户数据有头部，该值表示它的最大负载字节数，该值假定头部仅包含CONCATENATED_8_BIT_REFENENCE元素。
public static final int MAX_USER_DATA_SEPTETS ：值为160 (0x000000a0) ，表示每个消息的最大负载septets数。
public static final int MAX_USER_DATA_SEPTETS_WITH_HEADER ：值为153 (0x00000099)，如果存在用户数据头部，则该值表示最大负载septets数该值假定头部仅包含CONCATENATED_8_BIT_REFENENCE元素。
```
嵌套枚举成员SmsMessage.MessageClass的枚举值：
```  
public static final SmsMessage.MessageClass CLASS_0
public static final SmsMessage.MessageClass CLASS_1
public static final SmsMessage.MessageClass CLASS_2
public static final SmsMessage.MessageClass CLASS_3
public static final SmsMessage.MessageClass CLASS_UNKNOWN
```
嵌套枚举成员SmsMessage.MessageClass的公有方法：
```  
public static SmsMessage.MessageClass valueOf (String name)：返回值的字符串的值
public static final MessageClass[]   values  ()：返回MessageClass的值数组
```
嵌套类成员SmsMessage.SubmitPdu的字段：
```  
public byte[] encodedMessage：编码了的消息
public byte[] encodedScAddress：编码的服务中心地址
```
嵌套类成员SmsMessage.SubmitPdu的公有方法：
```  
public String toString()
返回一个包含简单的、可读的这个对象的描述字符串。鼓励子类去重写这个方法，并提供实现对象的类型和数据。默认实现简单地连接类名、@、十六进制表示的对象哈希码，即下面的形式： getClass().getName() + '@' + Integer.toHexString(hashCode())
```
#### 8、SMS接收程序
当一个SMS消息被接收时，一个新的广播意图由android.provider.Telepony.SMS_RECEIVED动作触发。注意：这个一个字符串字面量（string  literal），但是SDK当前并没有包括这个字符串的引用，因此当要在应用程序中使用它时必须自己显示的指定它。现在我们就开始构建一个SMS接收程序：
1)、跟SMS发送程序类似，要在清单文件AndroidManifest.xml中指定权限允许接收SMS：<uses-permission android:name="android.permission.RECEIVER_SMS"/>
为了能够回发短信，还应该加上发送的权限。
2)、应用程序监听SMS意图广播，SMS广播意图包含了到来的SMS细节。我们要从其中提取出SmsMessage对象，这样就要用到pdu键提取一个SMS PDUs数组（protocol description units—封装了一个SMS消息和它的元数据），每个元素表示一个SMS消息。为了将每个PDU byte数组转化为一个SMS消息对象，需要调用SmsMessage.createFromPdu。
每个SmsMessage包含SMS消息的详细信息，包括起始地址（电话号码）、时间戳、消息体。下面编写一个接收短信的类SmsReceiver代码如下：
```  
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.os.Bundle;
import android.telephony.SmsManager;
import android.telephony.SmsMessage;
import android.widget.Toast;
public class SmsReceiver extends BroadcastReceiver {
	@Override
	public void onReceive(Context _context, Intent _intent) {
		if (_intent.getAction().equals(SMS_RECEIVER)) {
			SmsManager sms = SmsManager.getDefault();
			Bundle bundle = _intent.getExtras();
			if (bundle != null) {
				Object[] pdus = (Object[]) bundle.get("pdus");
				SmsMessage[] messages = new SmsMessage[pdus.length];
				for (int i = 0; i < pdus.length; i++)
					messages[i] = SmsMessage.createFromPdu((byte[]) pdus[i]);
				for (SmsMessage message : messages) {
					String msg = message.getMessageBody();
					String to = message.getOriginatingAddress();
					if (msg.toLowerCase().startsWith(queryString)) {
						String out = msg.substring(queryString.length());
						sms.sendTextMessage(to, null, out, null, null);

						Toast.makeText(_context, "success", Toast.LENGTH_LONG)
								.show();
					}
				}
			}
		}
	}
	private static final String queryString = "@echo";
	private static final String SMS_RECEIVER = "android.provider.Telephony.SMS_RECEIVED";
}
```
上面代码的功能是从接收到的广播意图中提取来电号码、短信内容，然后将短信加上@echo头部回发给来电号码，并在屏幕上显示一个Toast消息提示成功。
#### 9、另一种发送短信的方式：使用Intent
上篇我们使用SmsManager类实现了发送SMS的功能，且并没有用到内置的客户端。实际上，我们很少这样做，自己在应用程序中去完全实现一个完整的SMS客户端。相反我们会去利用它，将需要发送的内容和目的手机号传递给内置的SMS客户端，然后发送。
下面我就向大家介绍如何利用Intent实现利用将我们的东西传递给内置SMS客户端发送我们SMS。为了实现这个功能，就要用到startActivity("指定一个Intent")方法，且指定Intent的动作为Intent.ACTION_SENDTO，用sms：指定目标手机号，用sms_body指定信息内容。java源文件如下所示：
```  
import android.app.Activity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
public class TextMessage extends Activity {
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		btnSend = (Button) findViewById(R.id.btnSend);
		edtPhoneNo = (EditText) findViewById(R.id.edtPhoneNo);
		edtContent = (EditText) findViewById(R.id.edtContent);
		btnSend.setOnClickListener(new View.OnClickListener() {
			public void onClick(View v) {
				String phoneNo = edtPhoneNo.getText().toString();
				String message = edtContent.getText().toString();
				if (phoneNo.length() > 0 && message.length() > 0) {
					Intent smsIntent = new Intent(Intent.ACTION_SENDTO, Uri
							.parse("sms:" + edtPhoneNo.getText().toString()));
					smsIntent.putExtra("sms_body", edtContent.getText()
							.toString());
					TextMessage.this.startActivity(smsIntent);
				} else
					Toast.makeText(getBaseContext(),
							"Please enter both phone number and message.",
							Toast.LENGTH_SHORT).show();
			}
		});
	}
	private Button btnSend;
	private EditText edtPhoneNo;
	private EditText edtContent;
}
```
注意代码中的红色粗体部分，就是实现这个功能的核心代码！布局文件maim.xml和值文件string.xml跟上篇中的一样，这里不再累述。运行结果如下图：
![img](P)  
图2、程序主界面
点击send按钮之后，转到内置的SMS客户端并且将我们输入的值传入了，如下图：
![img](P)  
图3、内容传至内置SMS客户端
发送之后，5556号android模拟器会收到我们发送的消息，如下图：
![img](P)  
图5、发送之后5556号android模拟器收到消息
10、增强SMS为MMS
我们讲了这么多，都还只是实现了简单的发生SMS的功能，如果我们想发送图片、音频怎么办(⊙o⊙)？不急，现在我们就将第9节介绍的SMS发送程序改造为MMS。
我们可以附加一个文件到我们的消息做为附件发送，用Intent.EXTRA_STREAM和附件资源的Uri做为参数调用putExtra()方法，附加到信息。并设置Intent的类型为mime-type。要注意的是：内置的MMS并不包括一个ACTION_SENDTO动作的Intent接收器，我们需要使用的动作类型是ACTION_SEND，并且目标手机号不在是使用sms：而是address。主要代码如下：
```  
// Get the URI of a piece of media to attach.
Uri attached_Uri = Uri.parse("content://media/external/images/media/1");
// Create a new MMS intent
Intent mmsIntent = new Intent(Intent.ACTION_SEND, attached_Uri);
mmsIntent.putExtra("sms_body", edtContent.getText().toString());
mmsIntent.putExtra("address", edtPhoneNo.getText().toString());
mmsIntent.putExtra(Intent.EXTRA_STREAM, attached_Uri);
mmsIntent.setType("image/png");
startActivity(mmsIntent);
```
将这段代码替换第9节中的红色粗体代码，就完成而来一个MMS的构建。