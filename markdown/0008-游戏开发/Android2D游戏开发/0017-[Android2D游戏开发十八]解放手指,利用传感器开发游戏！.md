前两篇都是向大家介绍了很有意思的两种手势操作，嵌入我们游戏中，不得不说让游戏的自由度、可玩性和趣味性都增色不少！那么今天继续给大家介绍一亮点！传感器！
#### 一：什么是传感器：
所谓传感器能够探测如光、热、温度、重力、方向 等等的功能！
#### 二：Android中提供传感器有哪些：
1. 加速度传感器(重力传感器)
2. 陀螺仪传感器
3. 光传感器
5. 恒定磁场传感器
6. 方向传感器
7. 恒定的压力传感器
8. 接近传感器
9. 温度传感器
今天我们给大家介绍的是游戏开发中最最常见的，用到的频率最高的一种传感器，加速度传感器(重力传感器)!
因为模拟器无法测试，所以我用手机调试的~,先上两张截图；
![img](P)  
![img](P)  
```  
[Tags]/**
 [Tags]* @author android
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
				String temp_str = "android提示： ";
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
			Log.v("android", "draw is Error!");
		} finally {
			sfh.unlockCanvasAndPost(canvas);
		}
	}
	@Override
	public void run() {
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
#### 备注1:
SensorEventListener的onSensorChanged事件将返回SensorEvent对象，包含Sensor的最新数据，通过event.values获得一个float[]数组!对于不同的传感器类型，其数组包含的元素个数是不同的，重力传感器总是返回一个长度为3的数组，分别代表X、Y和Z方向的数值。Z轴表示了手机是屏幕朝上还是屏幕朝下; 这里还要注意你当前手机处于 纵向， 还是横向，因为这个会影响我们的X，Y表示的意思!  
#### 如果当前手机是纵向屏幕: 
```  
x>0 说明当前手机左翻 x<0右翻    
y>0 说明当前手机下翻 y<0上翻 
```
#### 如果当前手机是横向屏幕:    
```  
x>0 说明当前手机下翻 x<0上翻  
y>0 说明当前手机右翻 y<0左翻  
``` 
#### 我要提醒各位童鞋:    
1.要考虑玩家当前拿手机的姿势，例如竖屏，横屏    
2.根据横竖屏幕的不同，虽然屏幕坐标系会自动改变，但是传感器的值不会自动改变坐标系！所以为什么会横屏竖屏改变的时候我们从传感器中取出的值表示的动作不一样的原因！！！因此大家游戏开发的时候对于人物移动、图片移动等等操作的时候，手势X,Y的正负值代表什么一定要想清楚！否则玩家会玩着玩着吐的 (太晕了！)
#### 备注2 :
这里本应该arc_x+=x;但是因为当前我屏幕是纵向!造成x>0的手势表示玩家将手机左翻了，但是我们屏幕的圆形应该根据人的反转相对应的移动，那么这里玩家将手机左翻，我们就应该让原型的X坐标减少！所以这里写成了arc_x-=x;! 
#### 总结一下: 
对于传感器的虽然本章只是讲了一个重力传感器，但是一个足够了，因为如果你想使用其他的传感器，那么你只要以下步骤就OK：
1.利用 SensorManager.getDefaultSensor();传入一个你想要的传感器的参数得到其实例！
2.注册！
3.在监听器里处理事件！
OK！就是这么简单、 