一般的ProgressBar都只是一个光光的条（这里说的都是水平进度条），虽然比不用进度条时给用户的感觉要好，但是如果在形像化的东西上面再加上点文字，将进度描述量化，就可以让用户更加明白当前进度是多少了。
有了需求，就可以开始实现了。
这里的原理就是继承一个ProgressBar，然后重写里面的onDraw()方法。
不多说，直接上码。（下面代码中的 package hol.test; import就不写了） 
```  
public class MyProgress extends ProgressBar {
	String text;
	Paint mPaint;
	public MyProgress(Context context) {
		super(context);
		System.out.println("1");
		initText();
	}
	public MyProgress(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		System.out.println("2");
		initText();
	}
	public MyProgress(Context context, AttributeSet attrs) {
		super(context, attrs);
		System.out.println("3");
		initText();
	}
	@Override
	public synchronized void setProgress(int progress) {
		setText(progress);
		super.setProgress(progress);
	}
	@Override
	protected synchronized void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		// this.setText();
		Rect rect = new Rect();
		this.mPaint.getTextBounds(this.text, 0, this.text.length(), rect);
		int x = (getWidth() / 2) - rect.centerX();
		int y = (getHeight() / 2) - rect.centerY();
		canvas.drawText(this.text, x, y, this.mPaint);
	}
	// 初始化，画笔
	private void initText() {
		this.mPaint = new Paint();
		this.mPaint.setColor(Color.WHITE);
	}
	private void setText() {
		setText(this.getProgress());
	}
	// 设置文字内容
	private void setText(int progress) {
		int i = (progress * 100) / this.getMax();
		this.text = String.valueOf(i) + "%";
	}
}
```
这样一个可以满足我们基本需求的进度条就写好了。　　用的时候就可以直接在layout的XML里面加了，不过添加的写法稍微有点不同。标记名要写成这个自定义进度条的完整类名，就像下面这样。 
```  
<hol.test.MyProgress 
	android:id="@+id/pgsBar
	android:max="100
	android:layout_width="fill_parent
	android:layout_height="wrap_content
	style="?android:attr/progressBarStyleHorizontal
	android:visibility="visible" />
```
这样写后，可能会因为命名空间改变，下面属性无法用代码提示。一个简单的做法就是，先写一个正常的ProgressBar的标记，把属性写完后，再将ProgressBar替换为我们自定义的进度条的完整类名。　　
最后，使用方法就和普通的ProgressBar差不多了。 
```  
public class ProgressTest extends Activity {
	private MyProgress myProgress = null;
	private Handler mHandler;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		findView();
		setParam();
		addListener();
		mHandler = new Handler(new Callback() {
			@Override
			public boolean handleMessage(Message msg) {
				myProgress.setProgress(msg.what);
				return false;
			}
		});
	}
	private void findView() {
		btn_go = (Button) findViewById(R.id.btn_go);
		myProgress = (MyProgress) findViewById(R.id.pgsBar);
	}
	private void setParam() {
		btn_go.setText("开始");
	}
	private void addListener() {
		btn_go.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				new Thread(new Runnable() {
					@Override
					public void run() {
						for (int i = 0; i <= 50; i++) {
							mHandler.sendEmptyMessage(i * 2);
							try {
								Thread.sleep(80);
							} catch (InterruptedException e) {
								e.printStackTrace();
							}
						}
					}
				}).start();
			}
		});
	}
}
```
PS：刚开始本以为进度条的构造方法只需要重写那个最长参数的，也就是那个构造方法3，实际上我测试的时候基本都是运行的3方法。但有一次出错了，最后才找到系统居然用的1方法，所以没办法，直接把三个构造方法全重写了。如果有谁知道原因，麻烦告诉一下。进度条上的文字信息，不一定非要百分比，可以自由发挥。比如类似  “当前个数/总数”。
![img](P)  