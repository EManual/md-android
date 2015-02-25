有关android游戏开发中的Sensor感应示例今天我们将一起来讨论，对于目前最新的Android2.2平台而言仍然没有具体的感应判断逻辑，下面我们一起定义下常用的感应动作事件。首先eoeAndroid提醒大家由于是三轴的立体空间感应所以相对于轨迹球、导航键的上下左右外，还提供了前后的感应，所以我们定义最基本的六种空间方向。
```  
public static final int CWJ_UP = 0;
public static final int CWJ_DOWN = 1;
public static final int CWJ_LEFT = 2;
public static final int CWJ_RIGHT = 4;
public static final int CWJ_FORWARD = 8; //向前
public static final int CWJ_BACKWARD = 16; //向后
```
下面我们做精确的角度旋转修正值定义，我们用到yaw、pitch和roll，相信学过3D开发的网友不会对这些陌生的，我们就把他们对应为绕y、x、z轴的角度好了，如果你们没有学过3D相关的知识这里eoeAndroid推荐大家可以通过Cube例子自定义Render来观察这三个值对应立方体的旋转角度。
Yaw在(0,0,0)中， 以xOz的坐标平面中围绕y轴旋转，如果是负角则我们定义为CWJ_YAW_LEFT 即往左边倾斜，同理我们定义如下:
```  
public static final int CWJ_YAW_LEFT = 0;
public static final int CWJ_YAW_RIGHT = 1;
public static final int CWJ_PITCH_UP = 2;
public static final int CWJ_PITCH_DOWN = 4;
public static final int CWJ_ROLL_LEFT = 8;
public static final int CWJ_ROLL_RIGHT = 16;
```
我们通过加速感应器可以获得SensorEvent的四个值，今天eoeAndroid给大家一个简单示例，不考虑其他因素，在public int accuracy、public Sensor sensor 、public long timestamp和public final float[] values 中，我们获取values的浮点数组来判断方向。
```  
int eoeAndroid = CWJ_UP;// 向上
float ax = values[0];
float ay = values[1];
float az = values[2];
float absx = Math.abs(ax);
float absy = Math.abs(ay);
float absz = Math.abs(az);
if (absx > absy && absx > absz) {
	if (ax > 0) {
		eoeAndroid = CWJ_RIGHT;
	} else {
		eoeAndroid = CWJ_LEFT;
	}
} else if (absy > absx && absy > absz) {
	if (ay > 0) {
		eoeAndroid = CWJ_FORWARD;
	} else {
		eoeAndroid = CWJ_BACKWARD;
	}
} else if (absz > absx && absz > absy) {
	if (az > 0) {
		eoeAndroid = CWJ_UP;
	} else {
		eoeAndroid = CWJ_DOWN;
	}
} else {
	eoeAndroid = CWJ_UNKNOWN;
}
```