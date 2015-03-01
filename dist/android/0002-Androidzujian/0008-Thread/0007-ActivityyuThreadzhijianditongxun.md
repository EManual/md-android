在Android中要让Activity与Thread进行通讯 其实很简单。
重点就在于android.os.Handler、java.lang.Thread以及android.os.Message这三个类的整合应用
我们在Thread中可以通过Message来通知Handler，Handler在这里扮演着联系Acitivity与Thread之间的角色。
首先在Acitivity中我们要定义一个常量来作为判断标示
```  
private static final int GUINOTIFIER = 0x1234;
```
然后定义一些例子里面需要的属性
```  
public Calendar mCalendar;
public int mMinutes;
public int mHour;
public Handler mHandler;
private Thread mClockThread;
```
接着我们通过Handler来接收Thread所传递的信息
```  
mHandler = new Handler() {
	public void handleMessage(Message msg) {
		switch (msg.what) {
			case TestHandler.GUINOTIFIER://TestHandler是Activity的类名
				//得到Handle的通知了 这个时候你可以做相应的操作，本例是使用TextView来显示时间
				mTextView.setText(mHour + " : " + mMinutes);
				break;
		}
		super.handleMessage(msg);
	}
};
```
接下来我们自定义一个Thread
```  
class LooperThread extends Thread {
	public void run() {
		super.run();
		try {
			do {// 每间隔一秒取一次系统时间
				long time = System.currentTimeMillis();
				final Calendar mCalendar = Calendar.getInstance();
				mCalendar.setTimeInMillis(time);
				mHour = mCalendar.get(Calendar.HOUR);
				mMinutes = mCalendar.get(Calendar.MINUTE);
				Thread.sleep(1000);
				// 取得系统时间后发送消息给Handler
				Message m = new Message();
				m.what = Ex04_14.GUINOTIFIER;
				Ex04_14.this.mHandler.sendMessage(m);
			} while (!LooperThread.interrupted());// 当系统发出终端命令时停止循环
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```
最后我们启动线程
```  
mClockThread = new LooperThread();
mClockThread.start();
```
利用上面的例子我们可以扩展更多的应用，比如使用Thread进行上传下载操作，完成后通知主Activity等等