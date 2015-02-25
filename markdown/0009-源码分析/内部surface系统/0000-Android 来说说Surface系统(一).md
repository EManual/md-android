#### 一 目的
目的就是为了讲清楚Android中的Surface系统，大家耳熟能详的SurfaceFlinger到底是个什么东西，它的工作流程又是怎样的。当然，鉴于SurfaceFlinger的复杂性，我们依然将采用情景分析的办法，找到合适的切入点。
一个Activity是怎么在屏幕上显示出来的呢?我将首先把这个说清楚。
接着我们把其中的关键调用抽象在Native层，以这些函数调用为切入点来研究SurfaceFlinger。好了，开始我们的征途吧。
#### 二 Activity是如何显示的
最初的想法就是，Activity获得一块显存，然后在上面绘图，最后交给设备去显示。这个道理是没错，但是Android的SurfaceFlinger是在System Server进程中创建的，Activity一般另有线程，这之间是如何...如何挂上关系的呢?我可以先提前告诉大家，这个过程还比较复杂。
这里有个函数叫handleLaunchActivity。
```  
private final void handleLaunchActivity(ActivityRecord r,
		Intent customIntent) {
	Activity a = performLaunchActivity(r, customIntent);
	if (a != null) {
		r.createdConfig = new Configuration(mConfiguration);
		Bundle oldState = r.state;
		handleResumeActivity(r.token, false, r.isForward);
		---->调用handleResumeActivity
	}
}
```
handleLaunchActivity中会调用handleResumeActivity。
```  
final void handleResumeActivity(IBinder token, boolean clearHide,
		boolean isForward) {
	boolean willBeVisible = !a.mStartedActivity;
	if (r.window == null && !a.mFinished && willBeVisible) {
		r.window = r.activity.getWindow();
		View decor = r.window.getDecorView();
		decor.setVisibility(View.INVISIBLE);
		ViewManager wm = a.getWindowManager();
		WindowManager.LayoutParams l = r.window.getAttributes();
		a.mDecor = decor;
		l.type = WindowManager.LayoutParams.TYPE_BASE_APPLICATION;
		if (a.mVisibleFromClient) {
			a.mWindowAdded = true;
			wm.addView(decor, l); // 这个很关键。
		}
	}
}
```
上面addView那几行非常关键，它关系到咱们在Activity中setContentView后，整个Window到底都包含了些什么。我先告诉大家。所有你创建的View之上，还有一个DecorView，这是一个FrameLayout，另外还有一个PhoneWindow。上面这些东西的代码在framework/Policies/Base/Phone/com/android/Internal/policy/impl。这些隐藏的View的创建都是由你在Acitivty的onCreate中调用setContentView导致的。
```  
public void addContentView(View view, ViewGroup.LayoutParams params) {
	if (mContentParent == null) { // 刚创建的时候mContentParent为空
		installDecor();
	}
	mContentParent.addView(view, params);
	final Callback cb = getCallback();
	if (cb != null) {
		cb.onContentChanged();
	}
}
```
installDecor将创建mDecor和mContentParent。mDecor是DecorView类型，ContentParent是ViewGroup类型
```  
private void installDecor() {
	if (mDecor == null) {
		mDecor = generateDecor();
		mDecor.setDescendantFocusability(ViewGroup.FOCUS_AFTER_DESCENDANTS);
		mDecor.setIsRootNamespace(true);
	}
	if (mContentParent == null) {
		mContentParent = generateLayout(mDecor);
	}
}	
```
那么，ViewManager wm = a.getWindowManager()又返回什么呢?PhoneWindow从Window中派生，Acitivity创建的时候会调用它的setWindowManager。而这个函数由Window类实现。代码在framework/base/core/java/android/view/Window.java中：
```  
public void setWindowManager(WindowManager wm, IBinder appToken,
		String appName) {
	mAppToken = appToken;
	mAppName = appName;
	if (wm == null) {
		wm = WindowManagerImpl.getDefault();
	}
	mWindowManager = new LocalWindowManager(wm);
} 	
```
分析JAVA代码这个东西真的很复杂。mWindowManager的实现是LocalWindowManager，但由通过Bridge模式把功能交给WindowManagerImpl去实现了。真的很复杂!好了，我们回到wm.addView(decor, l)。最终会由WindowManagerImpl来完成addView操作，我们直接看它的实现好了。代码在framework/base/core/java/android/view/WindowManagerImpl.java：
```  
private void addView(View view, ViewGroup.LayoutParams params, boolean nest) {
	ViewRoot root; // ViewRoot，我们的主人公终于登场!
	synchronized (this) {
		root = new ViewRoot(view.getContext());
		root.mAddNesting = 1;
		view.setLayoutParams(wparams);
		if (mViews == null) {
			index = 1;
			mViews = new View[1];
			mRoots = new ViewRoot[1];
			mParams = new WindowManager.LayoutParams[1];
		} else {
		}
		index--;
		mViews[index] = view;
		mRoots[index] = root;
		mParams[index] = wparams;
	}
	root.setView(view, wparams, panelParentView);
}
```
/base/core/java/android/view/ViewRoot.java中：
```  
public final class ViewRoot extends Handler implements ViewParent, View.AttachInfo.Callbacks 
{ 
	private final Surface mSurface = new Surface(); 
}	
```
它竟然从handler派生，而ViewParent不过定义了一些接口函数罢了。看到Surface直觉上感到它和SurfaceFlinger有点关系。Surface代码在framework/base/core/java/android/view/Surface.java中，我们调用的是无参构造函数。
```  
public Surface() { 
	mCanvas = new CompatibleCanvas(); //就是创建一个Canvas!
}
```