第三部分 Camera的主要实现分析
3.1 JAVA程序部分
在packages/apps/Camera/src/com/android/camera/ 目录的Camera。java文件中，包含了对Camera的调用。
在Camera.java中包含对包的引用：
```  
import android.hardware.Camera.PictureCallback;
import android.hardware.Camera.Size;
```
在这里定义的Camera类继承了活动Activity类，在它的内部，包含了一个 android.hardware.Camera
```  
public class Camera extends Activity implements View.OnClickListener,
		SurfaceHolder.Callback {
	android.hardware.Camera mCameraDevice;
}
```
对Camera功能的一些调用如下所示：
```  
mCameraDevice.takePicture(mShutterCallback, mRawPictureCallback, mJpegPictureCallback);
mCameraDevice.startPreview()；
mCameraDevice.stopPreview()；
```
startPreview、stopPreview 和takePicture等接口就是通过JAVA本地调用（JNI）来实现的。
frameworks/base/core/java/android/hardware/目录中的Camera。java文件提供了一个JAVA类：Camera。
```  
public class Camera {
}
```
在这个类当中，大部分代码使用JNI调用下层得到，例如：
```  
public void setParameters(Parameters params) {
	Log.e(TAG, "setParameters()");
	// params.dump()；
	native_setParameters(params.flatten());
}
```
再者，例如以下代码：
```  
public final void setPreviewDisplay(SurfaceHolder holder) {
	setPreviewDisplay(holder.getSurface());
}
private native final void setPreviewDisplay(Surface surface);
```
两个setPreviewDisplay参数不同，后一个是本地方法，参数为Surface类型，前一个通过调用后一个实现，但自己的参数以SurfaceHolder为类型。
3.2 Camera的JAVA本地调用部分
Camera的JAVA本地调用（JNI）部分在frameworks/base/core/jni/目录的android_hardware_Camera。cpp中的文件中实现。
android_hardware_Camera。cpp之中定义了一个JNINativeMethod（JAVA本地调用方法）类型的数组gMethods，如下所示：
```  
static JNINativeMethod camMethods[] = { 
	{"native_setup","(Ljava/lang/Object;)V",(void*)android_hardware_Camera_native_setup },
	{"native_release","()V",(void*)android_hardware_Camera_release },
	{"setPreviewDisplay","(Landroid/view/Surface;)V",(void *)android_hardware_Camera_setPreviewDisplay },
	{"startPreview","()V",(void *)android_hardware_Camera_startPreview },
	{"stopPreview", "()V", (void *)android_hardware_Camera_stopPreview },
	{"setHasPreviewCallback","(Z)V",(void *)android_hardware_Camera_setHasPreviewCallback },
	{"native_autoFocus","()V",(void *)android_hardware_Camera_autoFocus },
	{"native_takePicture", "()V", (void *)android_hardware_Camera_takePicture },
	{"native_setParameters","(Ljava/lang/String;)V",(void *)android_hardware_Camera_setParameters },
	{"native_getParameters", "()Ljava/lang/String;",(void *)android_hardware_Camera_getParameters }
	};
```
JNINativeMethod的第一个成员是一个字符串，表示了JAVA本地调用方法的名称，这个名称是在JAVA程序中调用的名称；第二个成员也是一个字符串，表示JAVA本地调用方法的参数和返回值；第三个成员是JAVA本地调用方法对应的C语言函数。
register_android_hardware_Camera 函数将gMethods注册为的类"android/media/Camera"，其主要的实现如下所示。
```  
int register_android_hardware_Camera(JNIEnv *env) 
{
// Register native functions 
return AndroidRuntime::registerNativeMethods(env, "android/hardware/Camera", 
				camMethods, NELEM(camMethods));
}
```
"android/hardware/Camera"对应JAVA的类android.hardware.Camera。