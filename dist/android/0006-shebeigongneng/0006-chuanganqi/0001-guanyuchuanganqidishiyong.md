传感器类型：方向、加速度(重力)、光线、磁场、距离(临近性)、温度等。 
```  
方向传感器：Sensor.TYPE_ORIENTATION
加速度(重力)传感器：Sensor.TYPE_ACCELEROMETER
光线传感器: Sensor.TYPE_LIGHT
磁场传感器：Sensor.TYPE_MAGNETIC_FIELD
距离(临近性)传感器： Sensor.TYPE_PROXIMITY
温度传感器：Sensor.TYPE_TEMPERATURE	
//获取某种类型的感应器
Sensor sensor = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
//注册监听，获取传感器变化值
sensorManager.registerListener(listener, sensor, SensorManager.SENSOR_DELAY_GAME);
```
上面第三个参数为采样率：最快、游戏、普通、用户界面。当应用程序请求特定的采样率时，其实只是对传感器子系统的一个建议，不保证特定的采样率可用。
最快：SensorManager.SENSOR_DELAY_FASTEST
最低延迟，一般不是特别敏感的处理不推荐使用，该种模式可能造成手机电力大量消耗，由于传递的为原始数据，算法不处理好将会影响游戏逻辑和UI的性能。
游戏：SensorManager.SENSOR_DELAY_GAME
游戏延迟，一般绝大多数的实时性较高的游戏都使用该级别。
普通：SensorManager.SENSOR_DELAY_NORMAL 
标准延迟，对于一般的益智类或EASY级别的游戏可以使用，但过低的采样率可能对一些赛车类游戏有跳帧现象。
用户界面：SensorManager.SENSOR_DELAY_UI
一般对于屏幕方向自动旋转使用，相对节省电能和逻辑处理，一般游戏开发中我们不使用。