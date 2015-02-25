java代码：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
public class CameraTest_3 extends Activity implements OnClickListener {
	[Tags]/** Called when the activity is first created. */
	MySurfaceView mySurface;
	boolean isClicked = false;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		mySurface = new MySurfaceView(this);
		setContentView(mySurface);
		mySurface.setOnClickListener(this);
	}
	@Override
	public void onClick(View v) {
		if (!isClicked) {
			mySurface.tackPicture();
			isClicked = true;
		} else {
			mySurface.voerTack();
			isClicked = false;
		}
	}
}
```
这样就是实现了拍照的功能，那么怎样要图片保存呢？那么这是就需要在那个参数中的jpeg的方法里面进行处理了，那个方法的data参数，就是相片的数据。
我们通过BitmapFactory。decodeByteArray(data, 0, data.length)来获得图片并通过io处理，将图片保存到想要保存的位置。
下面这段代码，是将照片保存到/sdcard/wjh.jpg；并把一些没有用到的代码全部删掉，剩下一些必须的代码
```  
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
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
	private PictureCallback jpeg = new PictureCallback() {
		@Override
		public void onPictureTaken(byte[] data, Camera camera) {
			try {
				Bitmap bm = BitmapFactory.decodeByteArray(data, 0, data.length);
				File file = new File("/sdcard/wjh.jpg");
				BufferedOutputStream bos = new BufferedOutputStream(
						new FileOutputStream(file));
				bm.compress(Bitmap.CompressFormat.JPEG, 100, bos);
				bos.flush();
				bos.close();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	};
	public MySurfaceView(Context context) {
		super(context);
		holder = getHolder();// 获得surfaceHolder引用
		holder.addCallback(this);
		holder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);// 设置类型
	}
	public void tackPicture() {
		myCamera.takePicture(null, null, jpeg);
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
			myCamera = Camera.open();// 开启相机,不能放在构造函数中，不然不会显示画面.
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