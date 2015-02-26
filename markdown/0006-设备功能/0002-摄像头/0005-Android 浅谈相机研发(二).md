java代码：
```  
import android.app.Activity;
import android.os.Bundle;
public class CameraTest_3 extends Activity {
	 /** Called when the activity is first created. */
	MySurfaceView mySurface;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		mySurface = new MySurfaceView(this);
		setContentView(mySurface);
	}
}
```
而且必须给应用添加权限:< uses-permission android:name="android.permission.CAMERA">< /uses-permission>
(5)能够预览了，接下来就是拍照了，拍照用到了一个camera.tackPiture()这个方法,这个方法，有三个参数分别是ShutterCallBack shutter,PictureCallBack raw,PictureCallBack jpeg.
```  
@Override
public void onShutter() {
	Log.d("ddd", "shutter");
}

private PictureCallback raw = new PictureCallback() {
	@Override
	public void onPictureTaken(byte[] data, Camera camera) {
		Log.d("ddd", "raw");
	}
};
private PictureCallback jpeg = new PictureCallback() {
	@Override
	public void onPictureTaken(byte[] data, Camera camera) {
		Log.d("ddd", "jpeg");
	}
};
```
当开始拍照时，会依次调用shutter的onShutter()方法，raw的onPictureTaken方法,jpeg的onPictureTaken方法.三个参数的作用是shutter--拍照瞬间调用，raw--获得没有压缩过的图片数据，jpeg---返回jpeg的图片数据
当你不需要对照片进行处理，可以直接用null代替.注意，当调用camera.takePiture方法后，camera关闭了预览，这时需要调用startPreview()来重新开启预览。
我用以上知识，加到上面的那个例子，就形成了下面的代码：
```  
import java.io.IOException;
import android.content.Context;
import android.graphics.PixelFormat;
import android.hardware.Camera;
import android.hardware.Camera.PictureCallback;
import android.hardware.Camera.ShutterCallback;
import android.util.Log;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
public class MySurfaceView extends SurfaceView implements
		SurfaceHolder.Callback {
	SurfaceHolder holder;
	Camera myCamera;
	private ShutterCallback shutter = new ShutterCallback() {
		@Override
		public void onShutter() {
			Log.d("ddd", "shutter");
		}
	};
	private PictureCallback raw = new PictureCallback() {
		@Override
		public void onPictureTaken(byte[] data, Camera camera) {
			Log.d("ddd", "raw");
		}
	};
	private PictureCallback jpeg = new PictureCallback() {
		@Override
		public void onPictureTaken(byte[] data, Camera camera) {
			Log.d("ddd", "jpeg");
		}
	};
	public MySurfaceView(Context context) {
		super(context);
		holder = getHolder();// 获得surfaceHolder引用
		holder.addCallback(this);
		holder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);// 设置类型
	}
	public void tackPicture() {
		myCamera.takePicture(null, null, null);
	}
	public void voerTack() {
		myCamera.startPreview();
	}
	@Override
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
		myCamera.startPreview();
	}
	@Override
	public void surfaceCreated(SurfaceHolder holder) {
		if (myCamera == null) {
			myCamera = Camera.open();// 开启相机,不能放在构造函数中,不然不会显示画面.
			try {
				myCamera.setPreviewDisplay(holder);
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
	@Override
	public void surfaceDestroyed(SurfaceHolder holder) {
		myCamera.stopPreview();// 停止预览
		myCamera.release();// 释放相机资源
		myCamera = null;
	}
}
```