SeekBar本身有就是一个View对象，既然是View对象那也证明它有setOnLongClickListener事件，众所周知，这个事件是让一个View对象拥有长按功能，进而达到我们想要实现的操作。
为SeekBar 添加长按事件监听如下：
```  
SeekBar.setOnLongClickListener(new OnLongClickListener() {
	@Override
	public boolean onLongClick(View v) {
		return false;
	}
});
```
那么，我们来试试，它是否可以成立 ，为它添加一个Toast ：
```  
Toast.makeText(ttActivty.this, "fda", 100).show();
```
一般来说，我们运行项目，然后长按SeekBar 即能够把Toast Show 出来。那么试一下吧，试了之后结果很让人费解，因为我们知道SeekBar 继承自AbsSeekBar 而AbsSeekBar 又是继承自ProgressBar ，而ProgressBar 的长按事件是可行的，可是这里我们同样的操作却得不到效果，Toast 并不能像我们如期想像的一样显示出来。那是不是就是说SeekBar 不支持长按事件呢？这点我也不好下定论，总之我们为其他同样的View 对象的操作是可行的。好了，既然SeekBar 可能不支持长按事件，那我们就为它模拟一个长按事件，模拟思路如下：
1、继承SeekBar 重写内部的事件和方法
2、实现一个线程，当在规定的时间内停住即认为其是一个长按动作
3、实现长按事件的接口函数
4、并为重写的SeekBar 添加自己的OnSeekBarChangeListener 监听事件
具体操作看如下：
1、继承SeekBar 重写内部的事件和方法
代码如下：
```  
public class seekBarDemo extends SeekBar implements OnTouchListener
```
2、实现一个线程，当在规定的时间内停住即认为其是一个长按动作
这里实现一个自己的Runable 对象，向Handler 对象发送消息，代码如下：
```  
/**
  * 为runable 赋值
  */
runable = new Runnable() {
	@Override
	public void run() {
		do {
			i++;
			try {
				Thread.sleep(400);
				Message msg = hand.obtainMessage();
				msg.arg1 = i;
				msg.sendToTarget();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		} while (isStop);
	}
};
```
之后实现一个handler 对象用来设置该重写View 具有的长按事件
3、实现长按事件的接口函数
```  
/**
  * 获取一个handler 对象
  * @param 0代表onTouch 1代表onChange
  * @param 视图对象
  * @param 进度
  * @return 返回一个handler对象
  */
public Handler getHandler(final int j, final View v, final int progress) {
	Handler h = new Handler() {
		@Override
		public void handleMessage(Message msg) {
			switch (j) {
			case 0:
				if (msg.arg1 == 3) {
					if (longClick != null) {
						longClick.onLongClick(v);
					}
				}
				break;
			case 1:
				if (msg.arg1 == 1) {
					pp = progress;
				}
				if (msg.arg1 == 2) {
					if (pp != progress) {
						i = 0;
					}
				}
				if (msg.arg1 == 3) {
					i = 0;
					if (pp == progress) {
						if (longClick != null) {
							longClick.onLongClick(seekBarDemo.this);
						}
					}
				}
				break;
			}
			super.handleMessage(msg);
		}
	};
	return h;
}
```
4、并为重写的SeekBar 添加自己的OnSeekBarChangeListener 监听事件
这里我们首先定义一个onChange接口，接口中有三未实现的方法，分别代表SeekBar的三种状态
```  
/**
  * 进度改变接口
  */
public interface onChange {
	public void onStopTrackingTouch(seekBarDemo seekBar);
	public void onStartTrackingTouch(seekBarDemo seekBar);
	public void onProgressChanged(seekBarDemo seekBar, int progress,
			boolean fromUser);
}
```
实现接口函数：
```  
this.setOnSeekBarChangeListener(new OnSeekBarChangeListener() {
	@Override
	public void onStopTrackingTouch(SeekBar seekBar) {
		if (SeekBarChange != null) {
			SeekBarChange.onStopTrackingTouch(seekBarDemo.this);
		}
	}
	@Override
	public void onStartTrackingTouch(SeekBar seekBar) {
		if (SeekBarChange != null) {
			SeekBarChange.onStartTrackingTouch(seekBarDemo.this);
		}
	}
	@Override
	public void onProgressChanged(final SeekBar seekBar,
			final int progress, boolean fromUser) {
		if (SeekBarChange != null) {
			SeekBarChange.onProgressChanged(seekBarDemo.this, progress,
					fromUser);
		}
		hand = getHandler(1, seekBarDemo.this, progress);
	}
});
```
完整的参考代码如下：
```  
import android.content.Context;
import android.os.Handler;
import android.os.Message;
import android.util.AttributeSet;
import android.view.MotionEvent;
import android.view.View;
import android.view.View.OnTouchListener;
import android.widget.SeekBar;
public class seekBarDemo extends SeekBar implements OnTouchListener {
	private onLong longClick;
	 /**
	  * 长按接口
	  */
	public interface onLong {
		public boolean onLongClick(View v);
	}
	private onChange SeekBarChange;
	 /**
	  * 进度改变接口
	  */
	public interface onChange {
		public void onStopTrackingTouch(seekBarDemo seekBar);
		public void onStartTrackingTouch(seekBarDemo seekBar);
		public void onProgressChanged(seekBarDemo seekBar, int progress,
				boolean fromUser);
	}
	private Handler hand;
	private Runnable runable;
	private Thread th;
	public static int i = 0;
	private boolean isStop = false;
	public static int pp = 0;
	public int index = 0;
	public seekBarDemo(Context context) {
		this(context, null);
	}
	public seekBarDemo(Context context, AttributeSet attrs) {
		super(context, attrs);
		this.setOnTouchListener(this);
		this.setOnSeekBarChangeListener(new OnSeekBarChangeListener() {
			@Override
			public void onStopTrackingTouch(SeekBar seekBar) {
				if (SeekBarChange != null) {
					SeekBarChange.onStopTrackingTouch(seekBarDemo.this);
				}
			}
			@Override
			public void onStartTrackingTouch(SeekBar seekBar) {
				if (SeekBarChange != null) {
					SeekBarChange.onStartTrackingTouch(seekBarDemo.this);
				}
			}
			@Override
			public void onProgressChanged(final SeekBar seekBar,
					final int progress, boolean fromUser) {
				if (SeekBarChange != null) {
					SeekBarChange.onProgressChanged(seekBarDemo.this, progress,
							fromUser);
				}
				hand = getHandler(1, seekBarDemo.this, progress);
			}
		});
		 /**
		  * 为runable 赋值
		  */
		runable = new Runnable() {
			@Override
			public void run() {
				do {
					i++;
					try {
						Thread.sleep(400);
						Message msg = hand.obtainMessage();
						msg.arg1 = i;
						msg.sendToTarget();
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				} while (isStop);
			}
		};
	}
	 /**
	  * 获取一个handler 对象
	  * @param 0代表onTouch 1代表onChange
	  * @param 视图对象
	  * @param 进度
	  * @return 返回一个handler对象
	  */
	public Handler getHandler(final int j, final View v, final int progress) {
		Handler h = new Handler() {
			@Override
			public void handleMessage(Message msg) {
				switch (j) {
				case 0:
					if (msg.arg1 == 3) {
						if (longClick != null) {
							longClick.onLongClick(v);
						}
					}
					break;
				case 1:
					if (msg.arg1 == 1) {
						pp = progress;
					}
					if (msg.arg1 == 2) {
						if (pp != progress) {
							i = 0;
						}
					}
					if (msg.arg1 == 3) {
						i = 0;
						if (pp == progress) {
							if (longClick != null) {
								longClick.onLongClick(seekBarDemo.this);
							}
						}
					}
					break;
				}
				super.handleMessage(msg);
			}
		};
		return h;
	}
	 /**
	  * 设置长按事件
	  * @param longClick
	  */
	public void setOnLongSeekBarClick(onLong longClick) {
		this.longClick = longClick;
	}
	 /**
	  * 设置进度改变事件
	  * @param change
	  */
	public void setOnSeekBarChange(onChange change) {
		this.SeekBarChange = change;
	}
	@Override
	public boolean onTouch(final View v, MotionEvent event) {
		switch (event.getAction()) {
		case MotionEvent.ACTION_DOWN:
			isStop = true;
			th = new Thread(runable);
			th.start();
			i = 0;
			hand = getHandler(0, v, 0);
			break;
		case MotionEvent.ACTION_UP:
			isStop = false;
			break;
		}
		return false;
	}
}
```
在布局的XML可以如下定义：
```  
<org.lytsing.android.qzoneloading.seekBarDemo
	android:max="200" android:id="@+id/seek"
	android:layout_width="fill_parent"
	android:layout_height="wrap_content">
</org.lytsing.android.qzoneloading.seekBarDemo>
```
功能至此完成，在前台可以像我们之前使用SeekBar一样使用，功能一样，大家也可以根据自己的需求再另行扩展。这里给出的只是一个思路。