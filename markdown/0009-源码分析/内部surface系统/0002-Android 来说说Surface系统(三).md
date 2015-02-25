我们赶紧转到WindowManagerService去看看吧。
```  
public int relayoutWindow(Session session, IWindow client, 
WindowManager.LayoutParams attrs, int requestedWidth, int requestedHeight, int viewVisibility, boolean insetsPending, Rect outFrame, Rect outContentInsets, Rect outVisibleInsets, Configuration outConfig, Surface outSurface){ 
	try { 
		//其中win是我们最初创建的WindowState！ 
		Surface surface = win.createSurfaceLocked();
		if (surface != null) { 
			//先创建一个本地surface，然后把传入的参数outSurface copyFrom一下 
			outSurface.copyFrom(surface);
			win.mReportDestroySurface = false;
			win.mSurfacePendingDestroy = false;
		} else { 
			outSurface.release();
		} 
	} 
} 
Surface createSurfaceLocked() { 
try { 
	mSurface = new Surface(
	mSession.mSurfaceSession, mSession.mPid, mAttrs.getTitle().toString(), 0, w, h, mAttrs.format, flags);
} 
Surface.openTransaction();
```
这里使用了Surface的另外一个构造函数。
```  
public Surface(SurfaceSession s, 
int pid, String name, int display, int w, int h, int format, int flags) 
throws OutOfResourcesException { 
	mCanvas = new CompatibleCanvas();
	init(s,pid,name,display,w,h,format,flags); ---->调用了native的init函数。
	mName = name;
} 
```
上面两个类的JNI实现都在framework/base/core/jni/android_view_Surface.cpp中。
```  
public class SurfaceSession { 
[Tags]/** Create a new connection with the surface flinger. */
public SurfaceSession() { 
	init();
}
```
它的init函数对应为：
```  
static void SurfaceSession_init(JNIEnv* env, jobject clazz) 
{ 
	//SurfaceSession对应为SurfaceComposerClient 
	sp client = new SurfaceComposerClient;
	client->incStrong(clazz);
	//Google常用做法，在JAVA对象中保存C++对象的指针。 
	env->SetIntField(clazz, sso.client, (int)client.get());
}
```
Surface的init对应为：
```  
staticvoid Surface_init( 
JNIEnv* env, jobject clazz, jobject session, jint pid, jstring jname, jint dpy, jint w, jint h, jint format, jint flags) { 
SurfaceComposerClient* client = (SurfaceComposerClient*)env->GetIntField(session, sso.client); 
sp surface; 
if (jname == NULL) { 
	//client是SurfaceComposerClient，返回的surface是一个SurfaceControl 
	//真得很复杂! 
	surface = client->createSurface(pid, dpy, w, h, format, flags);
} else { 
	const jchar* str = env->GetStringCritical(jname, 0);
	const String8 name(str, env->GetStringLength(jname));
	env->ReleaseStringCritical(jname, str);
	surface = client->createSurface(pid, name, dpy, w, h, format, flags);
} 
//把surfaceControl信息设置到Surface对象中 
setSurfaceControl(env, clazz, surface); 
}
```
这里仅仅是surfaceControl的转移，但是并没有看到Surface相关的信息。那么Surface在哪里创建的呢?为了解释这个问题，我使用了终极武器，aidl。
1 终极武器AIDL
aidl可以把XXX.aidl文件转换成对应的java文件。我们刚才调用的是WindowSession的relayOut函数。如下：
```  
sWindowSession.relayout( 
mWindow, params, (int) (mView.mMeasuredWidth * appScale + 0.5f), (int) (mView.mMeasuredHeight * appScale + 0.5f), viewVisibility, insetsPending, mWinFrame, mPendingContentInsets, mPendingVisibleInsets, mPendingConfiguration, mSurface);
```
它的aidl文件在framework/base/core/java/android/view/IWindowSession.aidl中
```  
interface IWindowSession { 
int add(IWindow window, in WindowManager.LayoutParams attrs, in int viewVisibility, out Rect outContentInsets); 
void remove(IWindow window); 
//注意喔，这个outSurface前面的是out，表示输出参数，这个类似于C++的引用。 
int relayout(IWindow window, in WindowManager.LayoutParams attrs, int requestedWidth, int requestedHeight, int viewVisibility, boolean insetsPending, out Rect outFrame, out Rect outContentInsets,out Rect outVisibleInsets, out Configuration outConfig, out Surface outSurface);
```
刚才说了，JNI及其JAVA调用只是copyFrom了SurfaceControl对象到outSurface中，但是没看到哪里创建Surface。这其中的奥秘就在aidl文件编译后生成的java文件中。
你在命令行下可以输入：
```  
aidl -Id:\android-2.2-froyo-20100625-source\source\frameworks\base\core\java\ -Id:\android-2.2-froyo-20100625-source\source\frameworks\base\Graphics\java d:\android-2.2-froyo-20100625source\source\frameworks\base\core\java\android\view\IWindowSession.aidl test.java
```
以生成test.java文件。-I参数指定include目录，例如aidl有些参数是在别的java文件中指定的，那么这个-I就需要把这些目录包含进来。
先看看ViewRoot这个客户端生成的代码是什么。
```  
public int relayout(android.view.IWindow window,
		android.view.WindowManager.LayoutParams attrs, int requestedWidth,
		int requestedHeight, int viewVisibility, boolean insetsPending,
		android.graphics.Rect outFrame,
		android.graphics.Rect outContentInsets,
		android.graphics.Rect outVisibleInsets,
		android.content.res.Configuration outConfig,
		android.view.Surface outSurface) throws android.os.RemoteException {
	android.os.Parcel _data = android.os.Parcel.obtain();
	android.os.Parcel _reply = android.os.Parcel.obtain();
	int _result;
	try {
		_data.writeInterfaceToken(DESCRIPTOR);
		_data.writeStrongBinder((((window != null)) ? (window.asBinder())
				: (null)));
		if ((attrs != null)) {
			_data.writeInt(1);
			attrs.writeToParcel(_data, 0);
		} else {
			_data.writeInt(0);
		}
		_data.writeInt(requestedWidth);
		_data.writeInt(requestedHeight);
		_data.writeInt(viewVisibility);
		_data.writeInt(((insetsPending) ? (1) : (0)));
		// 奇怪，outSurface的信息没有写到_data中。那.....
		mRemote.transact(Stub.TRANSACTION_relayout, _data, _reply, 0);
		_reply.readException();
		_result = _reply.readInt();
		if ((0 != _reply.readInt())) {
			outFrame.readFromParcel(_reply);
		}
		if ((0 != _reply.readInt())) {
			outSurface.readFromParcel(_reply); // 从Parcel中读取信息来填充outSurface
		}
	} finally {
		_reply.recycle();
		_data.recycle();
	}
	return _result;
}
```