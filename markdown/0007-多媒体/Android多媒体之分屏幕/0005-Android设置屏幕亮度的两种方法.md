在0.9的版本中，android是调用IHardwareService来进行屏幕亮度调整的，但在1.0r2,1.0r1 以后的SDK中却找不到这个类。
这时又要翻出0.9的SDK了，在0.9 SDK中android.os包中有相关类。只要将这些相关类添加到项目类路径中，我们也可以用IHardwareService来调整屏幕亮度了。以下代码G1下测试通过。
```  
[Tags]/**
 [Tags]* 取得当前用户自定义的屏幕亮度
 [Tags]*/
private int getOldBrightness() {
	int brightness;
	try {
		brightness = Settings.System.getInt(getContentResolver(),
				Settings.System.SCREEN_BRIGHTNESS);
	} catch (SettingNotFoundException snfe) {
		brightness = 255;
	}
	return brightness;
}
[Tags]/**
 [Tags]* 设置屏幕亮度
 [Tags]*/
private void setBrightness(int brightness) {
	IHardwareService hardware = IHardwareService.Stub
			.asInterface(ServiceManager.getService("hardware"));
	if (hardware != null) {
		try {
			hardware.setScreenBacklight(brightness);
		} catch (RemoteException e) {
			e.printStackTrace();
		}
	}
}
```
注意brightness的值为0 - 255，0最暗，255最亮。
附件中给出一个国外网友整理过的IHardwareService包，也就是那个hardware09.jar了，只要将它添加到类路径就可以直接使用上面代码了。要那个包的，可以留邮箱，我发给你们。
不过这个方法我是严重不推荐的，我自己用2.2的版本测试了一下，发现setScreenBacklight这个方法报method not found exception 的错误。写程序吗，还是得用官方推荐的方法，如下:
在1.5SDK以上的版本中，可以使用更简单的方法实现：
```  
private void brightnessMax() {
	WindowManager.LayoutParams lp = getWindow().getAttributes();
	lp.screenBrightness = 1.0f;
	getWindow().setAttributes(lp);
}
```