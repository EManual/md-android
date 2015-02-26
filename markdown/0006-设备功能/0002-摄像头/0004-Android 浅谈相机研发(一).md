在android中应用相机功能，一般有两种：一种是直接调用系统相机，一种自己写的相机。
我将分别演示两种方式的使用：
第一种：是使用Intent跳转到系统相机,action为:android.media.action.STILL_IMAGE_CAMERA
```  
Intent intent = new Intent(); //调用照相机
intent.setAction("android.media.action.STILL_IMAGE_CAMERA");
startActivity(intent);

import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
public class CameraTest_2 extends Activity {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Intent intent = new Intent(); // 调用照相机
		intent.setAction("android.media.action.STILL_IMAGE_CAMERA");
		startActivity(intent);
	}
}
```
想要测试的，可以直接新建一个项目，并且把主activity的代码换成上面的，然后运行，我测试了一下，上面这个代码并不需要权限，毕竟只是调用系统自带的程序。
当然网上还有一些其他相关的调用方法，只要设置对了action,那么系统就会调用系统自带的相机。
第二种:
(1)首先我们要自己创建一个照相，必须考虑用什么控件显示照相机中的预览效果，显然android已经帮我们做好了选择，那就是SurfaceView，而控制SurfaceView则需要一个surfaceHolder，他是系统提供的一个用来设置surfaceView的一个对象，而它通过surfaceView.getHolder()这个方法来获得。而Camera提供一个setPreviewDisplay(SurfaceHolder)的方法来连接surfaceHolder，并通过他来控制surfaceView，而我们则使用android的Camera类提供了startPreview()和stopPreview()来开启和关闭预览。
关系如下：
```  
Camera -- -->SurfaceHolder------>SurfaceView
```
(2)知道怎么预览了，当然也要知道怎么开启相机.Camera.open()这是个静态方法，如果相机没有别人用着，则会返回一个相机引用，如果被人用着，则会抛出异常。很奇怪的是，这个方法，不能随便放，如放在构造方法或者onCreate()方法中，都会照成没有预览效果。
(3)SurfaceHolder.Callback，这是个holder用来显示surfaceView 数据的接口，他分别必须实现3个方法
surfaceCreated()这个方法是surface 被创建后调用的。
surfaceChanged()这个方法是当surfaceView发生改变后调用的。
surfaceDestroyed()这个是当surfaceView销毁时调用的。
surfaceHolde通过addCallBack()方法将响应的接口绑定到他身上。
surfaceHolder还必须设定一个setType()方法，查看api的时候，发现这个方法已经过时，但是没有写，又会报错。
(4)我用以上知识写了一个MySurfaceView类，他继承于SurfaceView，并在里面实现了照相机的预览功能。这个我觉得最简单的照相机预览代码:
```  
import java.io.IOException;
import android.content.Context;
import android.hardware.Camera;
import android.util.Log;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
public class MySurfaceView extends SurfaceView implements
		SurfaceHolder.Callback {
	SurfaceHolder holder;
	Camera myCamera;
	public MySurfaceView(Context context) {
		super(context);
		holder = getHolder();// 获得surfaceHolder引用
		holder.addCallback(this);
		holder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);// 设置类型
	}
	@Override
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
		myCamera.startPreview();
	}
	@Override
	public void surfaceCreated(SurfaceHolder holder) {
		if (myCamera == null) {
			myCamera = Camera.open();// 开启相机,不能放在构造函数中，不然不会显示画面.
			try {
				myCamera.setPreviewDisplay(holder);
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
	@Override
	public void surfaceDestroyed(SurfaceHolder holder) {
		// TODO Auto-generated method stub
		myCamera.stopPreview();// 停止预览
		myCamera.release();// 释放相机资源
		myCamera = null;
		Log.d("ddd", "4");
	}
}
```