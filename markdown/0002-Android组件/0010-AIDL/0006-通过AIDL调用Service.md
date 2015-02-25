在网上找的一些关于service的例子都比较简单，都是通过startService("action")启动service，然后通过stopService("service")停止service。只能启动和停止service没有发挥service的功能。下面我通过介绍关于AIDL启动service来控制音乐播放的例子来说明通过前台控制service的使用。
1.在工程的包中一个后缀为aidl的文件：
```  
IMusicControlService.aidl
```
package com.androidmanual.androidstud2.service;--------包名一定要和当前工程的包名一样哦！
```  
interface IMusicControlService
{
	voidplayMusic();-------->播放音乐
	voidstopMusic();------->停止播放音乐
}
```
点击保存后，在gen/上述包名的目录下就创建了一个IMusicControlService.java文件了
2.在res/layout目录下创建布局文件：
startserviceactivity.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/tv_main"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello"
        android:textSize="18px" />
    <Button
        android:id="@+id/btn_play"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="播放音乐" />
    <Button
        android:id="@+id/btn_stop"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="停止播放" />
</LinearLayout>
```
3.创建一个service类，在该类的内部实例化IMusicControlService中的playMusic()和stopMusic()接口
```  
private final IMusicControlService.Stub binder = new IMusicControlService.Stub() {
	@Override
	public void playMusic() throws RemoteException {
		player = MediaPlayer.create(ControlMusicService.this,
				R.raw.shanghaitan);
		player.start();
	}
	@Override
	public void stopMusic() throws RemoteException {
		if (player.isPlaying()) {
			player.stop();
		}
	}
};
```
在该类的onBind()方法中返回上面实例的binder，即returnbinder;
4.创建StartServiceActivity类继承Activity类，在该类中通过ServiceConnection和后台的service连接
```  
private final ServiceConnection serviceConnection = new ServiceConnection() {
	// 第一次连接service时会调用这个方法
	@Override
	public void onServiceConnected(ComponentName name, IBinder service) {
		iMusicControlService = IMusicControlService.Stub
				.asInterface(service);
	}
	// service断开的时候会调用这个方法
	@Override
	public void onServiceDisconnected(ComponentName name) {
		// TODO Auto-generated method stub
		System.out.println("service unconntection");
		iMusicControlService = null;
	}
};
```
在oncreate方法中绑定service：
```  
Intent intent= new Intent();
intent.setClass(StartServiceActivity.this,ControlMusicService.class);
bindService(intent,serviceConnection,Context.BIND_AUTO_CREATE);
```
在点击playmusic按钮被点击时，执行如下代码：
```  
iMusicControlService.playMusic();
```
在点击stopmusic按钮被点击时，执行如下代码：
```  
iMusicControlService.stopMusic();
unbindService(serviceConnection);
```
好了这样就通过在Activity中通过aidl控制service了。