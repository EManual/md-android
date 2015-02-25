代码如下：
```  
public class ActivityMain extends Activity {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		spark();
	}
	private int clo = 0;
	public void spark() {
		final TextView touchScreen = (TextView) findViewById(R.id.TextView01);// 获取页面textview对象
		Timer timer = new Timer();
		TimerTask taskcc = new TimerTask() {
			public void run() {
				runOnUiThread(new Runnable() {
					public void run() {
						if (clo == 0) {
							clo = 1;
							touchScreen.setTextColor(Color.TRANSPARENT); // 透明
						} else {
							if (clo == 1) {
								clo = 2;
								touchScreen.setTextColor(Color.RED);
							} else {
								clo = 0;
								touchScreen.setTextColor(Color.GREEN);
							}
						}
					}
				});
			}
		};
		timer.schedule(taskcc, 1, 300); // 参数分别是delay（多长时间后执行），duration（执行间隔）
	}
}
```
schedule(TimerTask task, long delay)的注释：Schedules the specified task for execution after the specified delay。大意是在延时delay毫秒后执行task。并没有提到重复执行schedule(TimerTask task, long delay, long period)注释：Schedules the specified task for repeated fixed-delay execution, beginning after the specified delay。大意是在延时delay毫秒后重复的执行task，周期是period毫秒。
这样问题就很明确schedule(TimerTask task, long delay)只执行一次，schedule(TimerTask task, long delay, long period)才是重复的执行。
关键的问题在于程序员误以为schedule就是重复的执行，而没有仔细的研究API，一方面也是英文能力不够，浏览API的过程中不能很快的理解到含义。