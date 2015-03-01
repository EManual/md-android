重力感应主要是依靠手机的加速度传感器(accelerometer)来实现
在Android的开发中一共有八种传感器但是不一定每一款真机都支持这些传感器。因为很多功能用户根本不care的所以可能开发商会把某些功能屏蔽掉。还是得根据真机的实际情况来做开发，今天我们主要来讨论加速度传感器的具体实现方式。
传感器名称如下：
```  
加速度传感器(accelerometer)
陀螺仪传感器(gyroscope)
环境光照传感器(light)
磁力传感器(magnetic field)
方向传感器(orientation)
压力传感器(pressure)
距离传感器(proximity)
温度传感器(temperature)
```
1.SensorMannager传感器管理对象
手机中的所有传感器都须要通过SensorMannager来访问，调用getSystemService(SENSOR_SERVICE)方法就可以拿到当前手机的传感器管理对象。
```  
SensorManager mSensorMgr = (SensorManager) getSystemService(SENSOR_SERVICE); 
```
2.实现SensorEventListener接口
说道SensorEventListener接口就不得不说SensorListener接口。在Android1.5一下是通过实现SensorListener接口来捕获手机传感器状态，但是在1.5以上如果实现这个接口系统会提示你这行代码已经过期。今天我们不讨论SensorListener因为它已经是过时的东西了。主要讨论一下SensorEventListener接口。我们须要实现SensorEventListener这个接口 onSensorChanged(SensorEvent event)方法来捕获手机传感器的状态，拿到手机 X轴Y轴Z轴三个方向的重力分量，有了这三个方向的数据重力感应的原理我们就已经学会了。
```  
public void onSensorChanged(SensorEvent e) { 
	float x = e.values[SensorManager.DATA_X]; 
	float y = e.values[SensorManager.DATA_Y]; 
	float z = e.values[SensorManager.DATA_Z]; 
} 
```
手机屏幕向左侧方当X轴就朝向天空，垂直放置 这时候 Y 轴 与 Z轴没有重力分量，因为X轴朝向天空所以它的重力分量则最大 。这时候X轴 Y轴 Z轴的重力分量的值分别为(10，0，0)。
手机屏幕向右侧方当X轴就朝向地面，垂直放置 这时候 Y 轴 与 Z轴没有重力分量，因为X轴朝向地面所以它的重力分量则最小 。这时候X轴 Y轴 Z轴的重力分量的值分别为(-10，0，0)。
手机屏幕垂直竖立放置方当Y轴就朝向天空，垂直放置 这时候 X 轴 与 Z轴没有重力分量，因为Y轴朝向天空所以它的重力分量则最大 。这时候X轴 Y轴 Z轴的重力分量的值分别为(0，10，0)。
手机屏幕垂直竖立放置方当Y轴就朝向地面，垂直放置 这时候 X 轴 与 Z轴没有重力分量，因为Y轴朝向地面所以它的重力分量则最小 。这时候X轴 Y轴 Z轴的重力分量的值分别为(0，-10，0)。
手机屏幕向上当Z轴就朝向天空，水平放置 这时候 X 轴与Y轴没有重力分量，因为Z轴朝向天空所以它的重力分量则最大 。这时候X轴 Y轴 Z轴的重力分量的值分别为(0，0，10)。
手机屏幕向上当Z轴就朝向地面，水平放置 这时候 X 轴与Y轴没有重力分量，因为Z轴朝向地面所以它的重力分量则最小 。这时候X轴 Y轴 Z轴的重力分量的值分别为(0，0，-10)。