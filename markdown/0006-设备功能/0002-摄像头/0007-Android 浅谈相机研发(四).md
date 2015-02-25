注意，这是必须添加在sd卡上写数据的权限
```  
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
(7)能够拍照了，这下子要考虑如何让图片更好看了，这显然是专业人士的强项，但是我们在程序上，也可以做一些处理，向上面的那些，因为我直接把surfaceView当做整体布局，就可能出现屏幕被拉开了，不是很好看，所以这时，就可以不要把surfaceView弄成整体布局，把他弄到到一个布局管理器，在设置相关的参数.
这是需要注意的是有些参数不能随便乱设，如以下代码：
```  
Camera.Parameters parames = myCamera.getParameters();//获得参数对象
parames.setPictureFormat(PixelFormat.JPEG);//设置图片格式
parames.setPreviewSize(640，480);//这里面的参数只能是几个特定的参数，否则会报错.(176*144，320*240，352*288，480*360，640*480)
myCamera.setParameters(parames);
```
还有自动对焦，当然有些手机没有这个功能，自动对焦是通过autoFocus()这个方法调用一个自动对焦的接口，并在里面进行处理。
注意，这个方法必须在startPreview()和stopPreview()中间。
AutoFocusCallback是自动对焦的接口，实现它必须实现public void onAutoFocus(boolean success， Camera camera)这个方法，所以我们可以将拍照方法放在这里面，然后对焦后再进行拍摄..效果会好很多。
注意自动对焦需要添加<uses-feature android：name="android.hardware.camera.autofocus" />
下面我叫直接把上面的使用例子直接写出。
```  
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import android.app.Activity;
import android.content.pm.ActivityInfo;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.PixelFormat;
import android.hardware.Camera;
import android.hardware.Camera.AutoFocusCallback;
import android.hardware.Camera.PictureCallback;
import android.os.Bundle;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.View;
import android.view.Window;
import android.view.SurfaceHolder.Callback;
import android.view.View.OnClickListener;
public class CameraTest_4 extends Activity implements Callback,
		OnClickListener, AutoFocusCallback {
	SurfaceView mySurfaceView;// surfaceView声明
	SurfaceHolder holder;// surfaceHolder声明
	Camera myCamera;// 相机声明
	String filePath = "/sdcard/wjh.jpg";// 照片保存路径
	boolean isClicked = false;// 是否点击标识
	// 创建jpeg图片回调数据对象
	PictureCallback jpeg = new PictureCallback() {
		@Override
		public void onPictureTaken(byte[] data, Camera camera) {
			try {// 获得图片
				Bitmap bm = BitmapFactory.decodeByteArray(data, 0, data.length);
				File file = new File(filePath);
				BufferedOutputStream bos = new BufferedOutputStream(
						new FileOutputStream(file));
				bm.compress(Bitmap.CompressFormat.JPEG, 100, bos);// 将图片压缩到流中
				bos.flush();// 输出
				bos.close();// 关闭
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	};
	[Tags]/** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_NO_TITLE);// 无标题
		// 设置拍摄方向
		this.setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
		setContentView(R.layout.main);
		// 获得控件
		mySurfaceView = (SurfaceView) findViewById(R.id.surfaceView1);
		// 获得句柄
		holder = mySurfaceView.getHolder();
		// 添加回调
		holder.addCallback(this);
		// 设置类型
		holder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
		// 设置监听
		mySurfaceView.setOnClickListener(this);
	}
	@Override
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
		// 设置参数并开始预览
		Camera.Parameters params = myCamera.getParameters();
		params.setPictureFormat(PixelFormat.JPEG);
		params.setPreviewSize(640, 480);
		myCamera.setParameters(params);
		myCamera.startPreview();
	}
	@Override
	public void surfaceCreated(SurfaceHolder holder) {
		// 开启相机
		if (myCamera == null) {
			myCamera = Camera.open();
			try {
				myCamera.setPreviewDisplay(holder);
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
}
```