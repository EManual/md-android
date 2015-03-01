通过对Handler的学习，使我们理解了线程的运行，当我们使用多个线程的时候，会感觉很乱，如果我们应用Handler，我们可以更加清晰的去进行更新。下面是利用线程Handler更新进度条的示例：
```  
public class Main extends Activity {
	 /** Called when the activity is first created. */
	ProgressBar pb1;
	Handler handle = new Handler();
	// 新建一个Handler对象
	Button b1;
	Button b2;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		pb1 = (ProgressBar) findViewById(R.id.pb1);
		pb1.setProgress(0);
		b1 = (Button) findViewById(R.id.b1);
		b1.setOnClickListener(b1Lis);
		b2 = (Button) findViewById(R.id.b2);
		b2.setOnClickListener(b2Lis);
	}
	private OnClickListener b1Lis = new OnClickListener() {
		@Override
		public void onClick(View v) {
			// TODO Auto-generated method stub
			handle.post(add);
			// 开始执行add
		}
	};
	private OnClickListener b2Lis = new OnClickListener() {
		@Override
		public void onClick(View v) {
			// TODO Auto-generated method stub
			handle.removeCallbacks(add);
			// 停止执行
			pb1.setProgress(0);
		}
	};
	int pro = 0;
	Runnable add = new Runnable() {
		// 定义add
		@Override
		public void run() {
			// TODO Auto-generated method stub
			pro = pb1.getProgress() + 1;
			pb1.setProgress(pro);
			setTitle(String.valueOf(pro));
			if (pro < 100) {
				handle.postDelayed(add, 500);
				// 如果进度小于100,，则延迟500毫秒后重复执行add
			}
		}
	};
}
```