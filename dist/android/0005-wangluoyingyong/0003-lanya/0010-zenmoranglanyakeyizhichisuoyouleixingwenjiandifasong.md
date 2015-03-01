大家都知道，一般来说，从文件管理器里，只能发送一些常见的格式的文件，比如图片，视频，文本文档，ppt等，那么蓝牙模块是怎么去判断文件格式的呢，我稍微去追踪了下源码，发现过程如下：
1.进入文件管理器,选择某个文件，然后在弹出菜单中选择发送；
2.这时，在文件管理器中，会使用type = mMimeTypes.getMimeType(file.getName())去获取到该文件的类型，
因为文件管理器一般都是各个厂家自己定制的，这里就不贴大段的文件管理器的代码了，获取到合法的类型之后，会传入intent中,如果不合法，将不会进入下面的步骤，直接返回“不支持的类型”
intent.setType(type);
3.之后这个intent会被蓝牙，emil以及mms等程序捕获到，选择蓝牙之后，蓝牙的OPP服务会对该文件名进行解析。
我对比过高通平台以及MTK平台，发现在OPP这块，代码基本一致，应该是继承的android原生代码，没太大改动
我就拿高通7X27的举例吧，在路径/packages_qc_gb_qrd7x27a/packages_qrd_7x27a/apps/Bluetooth/src/com/android/bluetooth/opp/BluetoothOppObexServerSession.JAVA中，重写了 onPut(Operation op) 
在该接口中，对文件名的合法性又进行了各种判断，比如判断文件名是否为空，后缀名是否为空，文件长度是否为0等，最后去判断后缀名是否合法
```  
extension = name.substring(dotIndex + 1).toLowerCase(); // 取到后缀名并全部转为小写
MimeTypeMap map = MimeTypeMap.getSingleton(); // 获取各种合法后缀名
type = map.getMimeTypeFromExtension(extension); // 判断是否合法
```
这一步判断完之后，还要进入下一步判断，判断该后缀名是否存在于黑名单之中，(比如禁止传输.apk后缀名之类的)
```  
// Reject policy: anything outside the "white list" plus unspecified
// MIME Types.
if (!pre_reject
		&& (mimeType == null || (!Constants.mimeTypeMatches(mimeType,
				Constants.ACCEPTABLE_SHARE_INBOUND_TYPES)))) {
	if (D)
		Log.w(TAG,
				"mimeType is null or in unacceptable list, reject the transfer");
	pre_reject = true;
	obexResponse = ResponseCodes.OBEX_HTTP_UNSUPPORTED_TYPE;
}
```
只有以上的全部都通过了，才说明该文件可以被通过蓝牙发送出去。