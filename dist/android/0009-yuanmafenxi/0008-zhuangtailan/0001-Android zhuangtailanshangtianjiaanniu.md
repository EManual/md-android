代码如下：
```  
<com.android.server.status.StatusBarView xmlns:android="http://schemas.android.com/apk/res/android"
    android:background="@drawable/statusbar_background"
    android:descendantFocusability="afterDescendants"
    android:focusable="true"
    android:orientation="vertical" >
    <LinearLayout
        android:id="@+id/icons"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="horizontal" >
        <LinearLayout
            android:id="@+id/statusButtons"
            android:layout_width="wrap_content"
            android:layout_height="fill_parent"
            android:layout_alignParentRight="true"
            android:orientation="horizontal" >
            <ImageButton
                android:id="@+id/Stat_Home_button"
                android:layout_width="wrap_content"
                android:layout_height="fill_parent"
                android:background="@drawable/stat_home_button" />
            <ImageButton
                android:id="@+id/Stat_vol_down_button"
                android:layout_width="wrap_content"
                android:layout_height="fill_parent"
                android:background="@drawable/stat_volume_down_button_up" />
            <ImageButton
                android:id="@+id/Stat_vol_raise_button"
                android:layout_width="wrap_content"
                android:layout_height="fill_parent"
                android:background="@drawable/stat_volume_raise_button_up" />
        </LinearLayout>
        <com.android.server.status.IconMerger
            android:id="@+id/notificationIcons"
            android:layout_width="0dip"
            android:layout_height="fill_parent"
            android:layout_alignParentLeft="true"
            android:layout_weight="1"
            android:gravity="center_vertical"
            android:orientation="horizontal"
            android:paddingLeft="6dip" />
        <LinearLayout
            android:id="@+id/statusIcons"
            android:layout_width="wrap_content"
            android:layout_height="fill_parent"
            android:layout_alignParentRight="true"
            android:gravity="center_vertical"
            android:orientation="horizontal"
            android:paddingRight="6dip" />
    </LinearLayout>
    <LinearLayout
        android:id="@+id/ticker"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:animationCache="false"
        android:orientation="horizontal"
        android:paddingLeft="6dip" >
        <ImageSwitcher
            android:id="@+id/tickerIcon"
            android:layout_width="wrap_content"
            android:layout_height="fill_parent"
            android:layout_marginRight="8dip" >
            <com.android.server.status.AnimatedImageView
                android:layout_width="25dip"
                android:layout_height="25dip" />
            <com.android.server.status.AnimatedImageView
                android:layout_width="25dip"
                android:layout_height="25dip" />
        </ImageSwitcher>
        <com.android.server.status.TickerView
            android:id="@+id/tickerText"
            android:layout_width="0dip"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:paddingRight="10dip"
            android:paddingTop="2dip" >
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:singleLine="true"
                android:textColor="ff000000" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:singleLine="true"
                android:textColor="ff000000" />
        </com.android.server.status.TickerView>
    </LinearLayout>
    <com.android.server.status.DateView
        android:id="@+id/date"
        android:layout_width="wrap_content"
        android:layout_height="25dp"
        android:background="@drawable/statusbar_background"
        android:gravity="center_vertical|left"
        android:paddingLeft="6px"
        android:paddingRight="6px"
        android:singleLine="true"
        android:textColor="ff000000"
        android:textSize="16sp"
        android:textStyle="bold" />
</com.android.server.status.StatusBarView>
```
添加
```  
import android.os.ServiceManager; 
import android.view.IWindowManager; 
import android.widget.ImageButton; 
```
添加定义控件：
```  
ImageButton homeButton;//对应 "@+id/Stat_Home_button" 
ImageButton volUpButton;//对应"@+id/Stat_vol_raise_button" 
ImageButton volDownButton;//对应 "@+id/Stat_vol_down_button" 
IWindowManager windowManager; 
```
修改makeStatusBarView函数
```  
private void makeStatusBarView(Context context) 
```
为
```  
private void makeStatusBarView(final Context context) 
```
makeStatusBarView函数内部添加每个按钮的Listener来监听onClick
```  
windowManager = IWindowManager.Stub.asInterface(ServiceManager.getService("window"));
homeButton = (ImageButton) sb.findViewById(R.id.Stat_Home_button);
homeButton.setOnClickListener(new View.OnClickListener() {
	public void onClick(View v) {
		doKey(KeyEvent.KEYCODE_HOME);
	}
});
volUpButton = (ImageButton) sb.findViewById(R.id.Stat_vol_raise_button);
volUpButton.setOnClickListener(new View.OnClickListener() {
	public void onClick(View v) {
		doKey(KeyEvent.KEYCODE_VOLUME_UP);
	}
});
volDownButton = (ImageButton) sb.findViewById(R.id.Stat_vol_down_button);
volDownButton.setOnClickListener(new View.OnClickListener() {
	public void onClick(View v) {
		doKey(KeyEvent.KEYCODE_VOLUME_DOWN);
	}
});
```
当按钮监听到onClick时就做相应的处理：
运行一个线程来把ACTION_DOWN和ACTION_UP事件injectKeyEvent,
看上去非常的简单，但是java在这里肯定做了不少事
```  
private void doKey(final int eventCode) {
	new Thread(new Runnable() {
		public void run() {
			long now = SystemClock.uptimeMillis();
			try {
				KeyEvent down = new KeyEvent(now, now,
						KeyEvent.ACTION_DOWN, eventCode, 0);
				KeyEvent up = new KeyEvent(now, now, KeyEvent.ACTION_UP,
						eventCode, 0);
				windowManager.injectKeyEvent(down, true);
				windowManager.injectKeyEvent(up, true);
			} catch (RemoteException e) {
				Slog.d("Input", "DeadOjbectException");
			}
		}
	}).start();
}
```
最后在interceptTouchEvent()函数里面判断一下
```  
if (SPEW) {
	Slog.d(TAG, "Touch: rawY=" + event.getRawY() + " event=" + event
			+ " mDisabled=" + mDisabled);
	if ((mDisabled & StatusBarManager.DISABLE_EXPAND) != 0) {
		return false;
	}
}
int x1 = (int) event.getRawX();
int Button_l = homeButton.getLeft();
int Button_r = homeButton.getRight();
if (x1 > Button_l && x1 < Button_r) {
	Slog.d("pressed homeButton and intercepted");
	return false;
}
Button_l = volUpButton.getLeft();
Button_r = volUpButton.getRight();
if (x1 > Button_l && x1 < Button_r) {
	Slog.d("pressed volUpButton and intercepted");
	return false;
}
Button_l = volDownButton.getLeft();
Button_r = volDownButton.getRight();
if (x1 > Button_l && x1 < Button_r) {
	Slog.d("pressed volDownButton and intercepted");
	return false;
}
final int statusBarSize = mStatusBarView.getHeight();
final int hitSize = statusBarSize * 2;
```