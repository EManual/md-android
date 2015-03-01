以下是我测试通过的代码。
sendWithChosenClient()能使系统弹出一个选择菜单，选择gmail, 短信或者其它的客户选发送带附件的消息。
sendWithEmailOnly()可以只调出email客户端，附上附件。
现在我想只调出短信/彩信发送界面，发送带附件的消息。感觉是如何设置Intent.setType(...)的问题，但是总是试不通。试过的有：
application/octet-stream
image/png
vnd.android-dir/mms-sms
```  
private void sendWithChosenClient() {
	Intent sendIntent = new Intent(Intent.ACTION_SEND);
	sendIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	// for sms/mms only
	sendIntent.putExtra("address", "10086");
	sendIntent.putExtra("sms_body", "See attached picture");
	// for email only
	String[] mailto = { "zx.zhangxiong@gmail.com" };
	sendIntent.putExtra(Intent.EXTRA_EMAIL, mailto);
	sendIntent.putExtra(Intent.EXTRA_SUBJECT, "sendEmail2");
	sendIntent.putExtra(Intent.EXTRA_TEXT, "sendEmail2 Text");
	// attachment
	String fileName = Environment.getExternalStorageDirectory().toString()
			+ File.separator + "tmp" + File.separator + "viewCapture.png";
	String url = "file://" + fileName;
	sendIntent.putExtra(Intent.EXTRA_STREAM, Uri.parse(url));
	sendIntent.setType("image/png");
	startActivity(sendIntent);
}
private void sendWithEmailOnly() {
	Intent sendIntent = new Intent(Intent.ACTION_SEND);
	sendIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	String[] mailto = { "zx.zhangxiong@gmail.com" };
	sendIntent.putExtra(Intent.EXTRA_EMAIL, mailto);
	sendIntent.putExtra(Intent.EXTRA_SUBJECT, "sendEmail2");
	sendIntent.putExtra(Intent.EXTRA_TEXT, "sendEmail2 Text");
	String fileName = Environment.getExternalStorageDirectory().toString()
			+ File.separator + "tmp" + File.separator + "viewCapture.png";
	String url = "file://" + fileName;
	sendIntent.putExtra(Intent.EXTRA_STREAM, Uri.parse(url));
	sendIntent.setType("application/octet-stream");
	startActivity(sendIntent);
}
```