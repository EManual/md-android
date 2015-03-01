真奇怪啊，Binder客户端这头竟然没有把outSurface的信息发过去。我们赶紧看看服务端。服务端这边处理是在onTranscat函数中。
```  
@Override
public boolean onTransact(int code, android.os.Parcel data,
		android.os.Parcel reply, int flags)
		throws android.os.RemoteException {
	switch (code) {
	case TRANSACTION_relayout: {
		data.enforceInterface(DESCRIPTOR);
		android.view.IWindow _arg0;
		android.view.Surface _arg10;
		// 刚才说了，Surface信息并没有传过来，那么我们在relayOut中看到的outSurface是怎么
		// 出来的呢?看下面这句，原来在服务端这边竟然new了一个新的Surface!!!
		_arg10 = new android.view.Surface();
		int _result = this.relayout(_arg0, _arg1, _arg2, _arg3, _arg4,
				_arg5, _arg6, _arg7, _arg8, _arg9, _arg10);
		reply.writeNoException();
		reply.writeInt(_result);
		// _arg10是copyFrom了，那怎么传到客户端呢?
		if ((_arg10 != null)) {
			reply.writeInt(1);// 调用Surface的writeToParcel，把信息加入reply
			_arg10.writeToParcel(reply,
					android.os.Parcelable.PARCELABLE_WRITE_RETURN_VALUE);
		}
		return true;
	}
	}
}
```
客户端虽然传了一个surface，但其实没传递给服务端。服务端调用writeToParcel，把信息写到Parcel中，然后数据传回客户端。客户端调用Surface的readFromParcel，获得surface信息。那就去看看writeToParcel吧。
```  
static void Surface_writeToParcel( 
JNIEnv* env, jobject clazz, jobject argParcel, jint flags) { 
	Parcel* parcel = (Parcel*)env->GetIntField(argParcel, no.native_parcel);
	const sp& control(getSurfaceControl(env, clazz));
	//还好，只是把数据序列化到Parcel中 
	SurfaceControl::writeSurfaceToParcel(control, parcel);
	if (flags & PARCELABLE_WRITE_RETURN_VALUE) { 
		setSurfaceControl(env, clazz, 0);
	} 
}
```
那看看客户端的Surface_readFromParcel吧。
```  
static void Surface_readFromParcel( 
JNIEnv* env, jobject clazz, jobject argParcel) 
{ 
	Parcel* parcel = (Parcel*)env->GetIntField( argParcel, no.native_parcel);
	//客户端这边还没有surface呢 
	const sp& control(getSurface(env, clazz));
	//不过我们看到希望了，根据服务端那边Parcel信息来构造一个新的surface 
	sp rhs = new Surface(*parcel);
	if (!Surface::isSameSurface(control, rhs)) { 
		setSurface(env, clazz, rhs); //把这个新surface赋给客户端。终于我们有了surface!
	} 
}
```
到此，我们终于七拐八绕的得到了surface，这其中经历太多曲折了。下一节，我们将精简这其中复杂的操作，统一归到Native层，以这样为切入点来了解Surface的工作流程和原理。
反正你知道ViewRoot调用了relayout后，Surface就真正从WindowManagerService那得到了。继续回到ViewRoot，其中还有一个重要地方是我们知道却不了解的。
```  
private void performTraversals() {
	final View host = mView;
	boolean initialized = false;
	boolean contentInsetsChanged = false;
	boolean visibleInsetsChanged;
	try {
		relayoutResult = relayoutWindow(params, viewVisibility,
				insetsPending);
		// relayoutWindow完后，我们得到了一个无比宝贵的Surface
		// 那我们画界面的地方在哪里?就在这个函数中，离relayoutWindow不远处。
		boolean cancelDraw = attachInfo.mTreeObserver.dispatchOnPreDraw();
		if (!cancelDraw && !newSurface) {
			mFullRedrawNeeded = false;
			draw(fullRedrawNeeded);
		}
	} catch (Exception e) {
	}
}
private void draw(boolean fullRedrawNeeded) {
	Surface surface = mSurface; // 嘿嘿，不担心了，surface资源都齐全了
	if (surface == null || !surface.isValid()) {
		return;
	}
	if (mAttachInfo.mViewScrollChanged) {
		mAttachInfo.mViewScrollChanged = false;
		mAttachInfo.mTreeObserver.dispatchOnScrollChanged();
	}
	int yoff;
	final boolean scrolling = mScroller != null
			&& mScroller.computeScrollOffset();
	if (scrolling) {
		yoff = mScroller.getCurrY();
	} else {
		yoff = mScrollY;
	}
	if (mCurScrollY != yoff) {
		mCurScrollY = yoff;
		fullRedrawNeeded = true;
	}
	float appScale = mAttachInfo.mApplicationScale;
	boolean scalingRequired = mAttachInfo.mScalingRequired;
	Rect dirty = mDirty;
	if (mUseGL) { // 我们不用OPENGL
	}
	Canvas canvas;
	try {
		int left = dirty.left;
		int top = dirty.top;
		int right = dirty.right;
		int bottom = dirty.bottom;
		// 从Surface中锁定一块区域，这块区域是我们认为的需要重绘的区域
		canvas = surface.lockCanvas(dirty);
		canvas.setDensity(mDensity);
	} catch (Exception e) {
	}
	try {
		if (!dirty.isEmpty() || mIsAnimating) {
			long startTime = 0L;
			try {
				canvas.translate(0, -yoff);
				if (mTranslator != null) {
					mTranslator.translateCanvas(canvas);
				}
				canvas.setScreenDensity(scalingRequired);
				// mView就是之前的decoreView,
				mView.draw(canvas);
			} finally {
				// 我们的图画完了，告诉surface释放这块区域
				surface.unlockCanvasAndPost(canvas);
			}
			if (scrolling) {
				mFullRedrawNeeded = true;
				scheduleTraversals();
			}
		}
	} finally {
	}
}
```