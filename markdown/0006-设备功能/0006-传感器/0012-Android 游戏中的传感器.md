一：什么是传感器：
所谓传感器能够探测如光、热、温度、重力、方向 等等的功能！
二：Android中提供传感器有哪些：
```  
1. 加速度传感器(重力传感器)
2. 陀螺仪传感器
3. 光传感器
5. 恒定磁场传感器
6. 方向传感器
7. 恒定的压力传感器
8. 接近传感器
9. 温度传感器
```
今天我们给大家介绍的是游戏开发中最最常见的，用到的频率最高的一种传感器，加速度传感器(重力传感器)!
```  
[Tags]/**
 [Tags]* @Sensor 加速度传感器 ,也称为重力传感器
 [Tags]* @SDK 1.5(api 3)就支持传感器了
 [Tags]* @解释：此传感器不仅对玩家反转手机的动作可以检测到，而且会根据反转手机的程度,得到传感器的值也会不同！
 [Tags]*/
public class MySurfaceView extends SurfaceView implements Callback, Runnable {
	private Thread th = new Thread(this);
	private SurfaceHolder sfh;
	private Canvas canvas;
	private Paint paint;
	private SensorManager sm;
	private Sensor sensor;
	private SensorEventListener mySensorListener;
	private int arc_x, arc_y;// 圆形的x,y位置
	private float x = 0, y = 0, z = 0;
	public MySurfaceView(Context context) {
		super(context);
		this.setKeepScreenOn(true);
		sfh = this.getHolder();
		sfh.addCallback(this);
		paint = new Paint();
		paint.setAntiAlias(true);
		setFocusable(true);
		setFocusableInTouchMode(true);
		// 通过服务得到传感器管理对象
		sm = (SensorManager) MainActivity.ma
				.getSystemService(Service.SENSOR_SERVICE);
		sensor = sm.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);// 得到一个重力传感器实例
		// TYPE_ACCELEROMETER 加速度传感器(重力传感器)类型。
		// TYPE_ALL 描述所有类型的传感器。
		// TYPE_GYROSCOPE 陀螺仪传感器类型
		// TYPE_LIGHT 光传感器类型
		// TYPE_MAGNETIC_FIELD 恒定磁场传感器类型。
		// TYPE_ORIENTATION 方向传感器类型。
		// TYPE_PRESSURE 描述一个恒定的压力传感器类型
		// TYPE_PROXIMITY 常量描述型接近传感器
		// TYPE_TEMPERATURE 温度传感器类型描述
		mySensorListener = new SensorEventListener() {
			@Override
			// 传感器获取值发生改变时在响应此函数
			public void onSensorChanged(SensorEvent event) {// 备注1
			// 传感器获取值发生改变，在此处理
				x = event.values[0]; // 手机横向翻滚
				// x>0 说明当前手机左翻 x<0右翻
				y = event.values[1]; // 手机纵向翻滚
				// y>0 说明当前手机下翻 y<0上翻
				z = event.values[2]; // 屏幕的朝向
				// z>0 手机屏幕朝上 z<0 手机屏幕朝下
				arc_x -= x;// 备注2
				arc_y += y;
			}

			@Override
			// 传感器的精度发生改变时响应此函数
			public void onAccuracyChanged(Sensor sensor, int accuracy) {
				// TODO Auto-generated method stub
			}
		};
		sm.registerListener(mySensorListener, sensor,
				SensorManager.SENSOR_DELAY_GAME);
		// 第一个参数是传感器监听器，第二个是需要监听的传感实例
		// 最后一个参数是监听的传感器速率类型： 一共一下四种形式
		// SENSOR_DELAY_NORMAL 正常
		// SENSOR_DELAY_UI 适合界面
		// SENSOR_DELAY_GAME 适合游戏 (我们必须选这个呀 哇哈哈~)
		// SENSOR_DELAY_FASTEST 最快
	}
	public void surfaceCreated(SurfaceHolder holder) {
		arc_x = this.getWidth() / 2 - 25;
		arc_y = this.getHeight() / 2 - 25;
		th.start();
	}
	public void draw() {
		try {
			canvas = sfh.lockCanvas();
			if (canvas != null) {
				canvas.drawColor(Color.BLACK);
				paint.setColor(Color.RED);
				canvas.drawArc(new RectF(arc_x, arc_y, arc_x + 50, arc_y + 50),
						0, 360, true, paint);
				paint.setColor(Color.YELLOW);
				canvas.drawText("当前重力传感器的值:", arc_x - 50, arc_y - 30, paint);
				canvas.drawText("x=" + x + ",y=" + y + ",z=" + z, arc_x - 50,
						arc_y, paint);
				String temp_str = "Himi提示： ";
				String temp_str2 = "";
				String temp_str3 = "";
				if (x < 1 && x > -1 && y < 1 && y > -1) {
					temp_str += "当前手机处于水平放置的状态";
					if (z > 0) {
						temp_str2 += "并且屏幕朝上";
					} else {
						temp_str2 += "并且屏幕朝下,提示别躺着玩手机，对眼睛不好哟~";
					}
				} else {
					if (x > 1) {
						temp_str2 += "当前手机处于向左翻的状态";
					} else if (x < -1) {
						temp_str2 += "当前手机处于向右翻的状态";
					}
					if (y > 1) {
						temp_str2 += "当前手机处于向下翻的状态";
					} else if (y < -1) {
						temp_str2 += "当前手机处于向上翻的状态";
					}
					if (z > 0) {
						temp_str3 += "并且屏幕朝上";
					} else {
						temp_str3 += "并且屏幕朝下,提示别躺着玩手机，对眼睛不好哟~";
					}
				}
				paint.setTextSize(20);
				canvas.drawText(temp_str, 0, 50, paint);
				canvas.drawText(temp_str2, 0, 80, paint);
				canvas.drawText(temp_str3, 0, 110, paint);
			}
		} catch (Exception e) {
			Log.v("Himi", "draw is Error!");
		} finally {
			sfh.unlockCanvasAndPost(canvas);
		}
	}
	@Override
	public void run() {
		// TODO Auto-generated method stub
		while (true) {
			draw();
			try {
				Thread.sleep(100);
			} catch (Exception ex) {
			}
		}
	}
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
	}
	public void surfaceDestroyed(SurfaceHolder holder) {
	}
}
```
SensorEventListener的onSensorChanged事件将返回SensorEvent对象，包含Sensor的最新数据，通过event.values获得一个float[]数组!对于不同的传感器类型，其数组包含的元素个数是不同的，重力传感器总是返回一个长度为3的数组，分别代表X、Y和Z方向的数值。Z轴表示了手机是屏幕朝上还是屏幕朝下;
这里还要注意你当前手机处于 纵向， 还是横向，因为这个会影响我们的X，Y表示的意思!
如果当前手机是纵向屏幕:
```  
x>0 说明当前手机左翻 x<0右翻     
y>0 说明当前手机下翻 y<0上翻
```
如果当前手机是横向屏幕:
```  
x>0 说明当前手机下翻 x<0上翻     
y>0 说明当前手机右翻 y<0左翻 
```