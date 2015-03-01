此函数回传JNI_VERSION_1_4值给VM，于是VM知道了其所使用的JNI版本了。此外，它也做了一些初期的动作(可呼叫任何本地函数)，例如指令：
```  
if (register_android_media_MediaPlayer(env) < 0) {
	LOGE("ERROR: MediaPlayer native registration failed\n");
	goto bail;
}
```
就将此组件提供的各个本地函数(Native Function)登记到VM里，以便能加快后续呼叫本地函数的效率。
JNI_OnUnload()函数与JNI_OnLoad()相对应的。在载入C组件时会立即呼叫JNI_OnLoad()来进行组件内的初期动作；而当VM释放该C组件时，则会呼叫JNI_OnUnload()函数来进行善后清除动作。当VM呼叫JNI_OnLoad()或JNI_Unload()函数 时，都会将VM的指针(Pointer)传递给它们，其参数如下：
```  
jint JNI_OnLoad(JavaVM* vm, void* reserved) {     }
jint JNI_OnUnload(JavaVM* vm, void* reserved){     }
```
在JNI_OnLoad()函数里，就透过VM之指标而取得JNIEnv之指标值，并存入env指标变数里，如下述指令：
```  
jint JNI_OnLoad(JavaVM* vm, void* reserved){
	JNIEnv* env = NULL;
	jint result = -1;
	if (vm->GetEnv((void**) &env, JNI_VERSION_1_4) != JNI_OK) {
		LOGE("ERROR: GetEnv failed\n");
		goto bail;
	}
}
```
由于VM通常是多执行绪(Multi-threading)的执行环境。每一个执行绪在呼叫JNI_OnLoad()时，所传递进来的JNIEnv指标值 都是不同的。为了配合这种多执行绪的环境，C组件开发者在撰写本地函数时，可藉由JNIEnv指标值之不同而避免执行绪的资料冲突问题，才能确保所写的本 地函数能安全地在Android的多执行绪VM里安全地执行。基于这个理由，当在呼叫C组件的函数时，都会将JNIEnv指标值传递给它，如下：
```  
jint JNI_OnLoad(JavaVM* vm, void* reserved)
{
	JNIEnv* env = NULL;
	if (register_android_media_MediaPlayer(env) < 0) {
	}
}
```
这JNI_OnLoad()呼叫register_android_media_MediaPlayer(env)函数时，就将env指标值传递过去。如此，在register_android_media_MediaPlayer()函数就能藉由该指标值而区别不同的执行绪，以便化解资料冲突的问题。
例如，在register_android_media_MediaPlayer()函数里，可撰写下述指令：
```  
if((*env)->MonitorEnter(env, obj) != JNI_OK) {
}
```
查看是否已经有其他执行绪进入此物件，如果没有，此执行绪就进入该物件里执行了。还有，也可撰写下述指令：
```  
if ((*env)->MonitorExit(env, obj) != JNI_OK) {
}
```
查看是否此执行绪正在此物件内执行，如果是，此执行绪就会立即离开。