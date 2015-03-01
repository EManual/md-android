从Android手机开始，主流的智能机纷纷加入了感应器Sensor硬件，常见的有光线感应器、重力感应器、加速感应器，而更高级的有磁极方向、陀螺仪、距离感应器、温度感应器等等。对于android游戏开发，我们主要用到重力、加速、磁力和陀螺仪四种，当然部分游戏可能需要GPS或Cellid定位来修正一些位移信息。从系统中提高的感应器主要在android.hardware中，我们可以看到系统提供了android.hardware.SensorEventListener、Sensor和SensorManager这三个类，我们会发现除了可以获取感应器的信息，和感应器的原始数据外，并没有提供相关的逻辑处理。Android123将会分3篇来详细的介绍不同感应器的作用和逻辑处理，比如自由落体，晃动，磁极，当前的旋转速度。
未来Android123将完成主要是一个基于OpenGL 3D的雷电游戏，最终加入联网对战效果可以团队打怪实现手机3D网游充分发挥Android手机的娱乐能力。对于大多数新款Android手机可能没有配备轨迹球或导航键的方向控制，所以重力感应器是这类实时性较强游戏的首选控制方式。主要有以下几点问题对于Sensor
1. 降噪处理，如果做过LBS软件的大家可能明白偏移修正，在GPS无法正常获取数据较间断时地图不能乱飘，这里Sensor也不例外，除了使用采样数据平均值获取外，可以间隔采样的方法来处理。细节的算法我们将在下节给出示例代码。
2. 感应器的敏感度，在Android中提供了四种延迟级别分别为
SENSOR_DELAY_FASTEST 最低延迟，一般不是特别敏感的处理不推荐使用，该种模式可能造成手机电力大量消耗，由于传递的为原始数据，算法不处理好将会影响游戏逻辑和UI的性能，所以Android开发网不推荐大家使用。
SENSOR_DELAY_GAME 游戏延迟，一般绝大多数的实时性较高的游戏都使用该级别。
int SENSOR_DELAY_NORMAL 标准延迟，对于一般的益智类或EASY级别的游戏可以使用，但过低的采样率可能对一些赛车类游戏有跳帧现象。
int SENSOR_DELAY_UI 用户界面延迟，一般对于屏幕方向自动旋转使用，相对节省电能和逻辑处理，一般游戏开发中我们不使用。
有关Android游戏开发中的Sensor感应示例今天我们将一起来讨论，对于目前最新的Android2.2平台而言仍然没有具体的感应判断逻辑，下面我们一起定义下常用的感应动作事件。首先Android123提醒大家由于是三轴的立体空间感应所以相对于轨迹球、导航键的上下左右外，还提供了前后的感应，所以我们定义最基本的六种空间方向。
```  
public static final int CWJ_UP = 0;
public static final int CWJ_DOWN = 1;
public static final int CWJ_LEFT = 2;
public static final int CWJ_RIGHT = 4;
public static final int CWJ_FORWARD = 8; //向前
public static final int CWJ_BACKWARD = 16; //向后
```
下面我们做精确的角度旋转修正值定义，我们用到yaw、pitch和roll，相信学过3D开发的网友不会对这些陌生的，我们就把他们对应为绕y、x、z 轴的角度好了，如果你们没有学过3D相关的知识这里Android开发网推荐大家可以通过Cube例子自定义Render来观察这三个值对应立方体的旋转角度。
Yaw在(0,0,0)中， 以xOz的坐标平面中围绕y轴旋转，如果是负角则我们定义为CWJ_YAW_LEFT 即往左边倾斜，同理我们定义如下: 
```  
public static final int CWJ_YAW_LEFT = 0;
public static final int CWJ_YAW_RIGHT = 1;
public static final int CWJ_PITCH_UP = 2;
public static final int CWJ_PITCH_DOWN = 4;
public static final int CWJ_ROLL_LEFT = 8;
public static final int CWJ_ROLL_RIGHT = 16;
```
我们通过加速感应器可以获得SensorEvent的四个值，今天Android123给大家一个简单示例，不考虑其他因素，在public int accuracy 、public Sensor sensor 、public long timestamp  和  public final float[] values 中，我们获取values的浮点数组来判断方向。 
```  
int nAndroid123 = CWJ_UP //向上
float ax = values[0];
float ay = values[1];
float az = values[2];
float absx = Math.abs(ax);
float absy = Math.abs(ay);
float absz = Math.abs(az);
if (absx > absy && absx > absz) {
	if (ax > 0) {
		nAndroid123 = CWJ_RIGHT;
	} else {
		nAndroid123 = CWJ_LEFT;
	}
} else if (absy > absx && absy > absz) {
	if (ay > 0) {
		nAndroid123= CWJ_FORWARD;
	} else {
		nAndroid123= CWJ_BACKWARD;
	}
} else if (absz > absx && absz > absy) {
	if (az > 0) {
		nAndroid123 = CWJ_UP;
	} else {
		nAndroid123 = CWJ_DOWN;
	}
} else {
	nAndroid123 = CWJ_UNKNOWN;
}
```
有关偏向角度问题，我们将在下一次详细讲述，对于一般的2D游戏，我们可以参考本文来实现重力控制，所以总体来说Android游戏开发比较简单易懂，Android平台使用的Java语言还是很适合做游戏的。在逻辑表达上更清晰。