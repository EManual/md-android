你后台的Activity被系统回收怎么办：onSaveInstanceState 
答：当你的程序中某一个Activity A 在运行时中，主动或被动地运行另一个新的Activity B这个时候A会执行 
```  
public void onSaveInstanceState(Bundle outState) {
	super.onSaveInstanceState(outState);
	outState.putLong("id", 1234567890);
}
public void onSaveInstanceState(Bundle outState) {
	super.onSaveInstanceState(outState);
	outState.putLong("id", 1234567890);
} 
```
B完成以后又会来找A,这个时候就有两种情况，一种是A被回收，一种是没有被回收，被回收的A就要重新调用onCreate()方法，不同于直接启动的是这回onCreate()里是带上参数 savedInstanceState，没被收回的就还是onResume就好了。 
savedInstanceState是一个Bundle对象，你基本上可以把他理解为系统帮你维护的一个Map对象。在onCreate()里你可能会用到它，如果正常启动onCreate就不会有它，所以用的时候要判断一下是否为空。 
```  
```  
if (savedInstanceState != null) {
	long id = savedInstanceState.getLong("id");
}
if (savedInstanceState != null) {
	long id = savedInstanceState.getLong("id");
}
```
就像官方的Notepad教程里的情况，你正在编辑某一个note，突然被中断，那么就把这个note的id记住，再起来的时候就可以根据这个id去把那个note取出来，程序就完整一些。这也是看你的应用需不需要保存什么，比如你的界面就是读取一个列表，那就不需要特殊记住什么，哦，没准你需要记住滚动条的位置... 