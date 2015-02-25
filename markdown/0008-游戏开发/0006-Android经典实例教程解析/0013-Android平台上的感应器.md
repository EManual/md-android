在Android 2.3中新增了3个感应器，对于Android平台的开发我们通过感应器可以发挥想象设计出一些很实用的软件。下面就一起看下目前API Level为9时一共11个感应器分辨是什么吧。
1. ACCELEROMETER 加速，描述加速度的。
2.GRAVITY 重力，这个在大家都知道。
3.GYROSCOPE 陀螺仪，对于物体跌落检测更强大些，开发游戏少了它会有点遗憾的，API Level 9新增的类型。
4. LIGHT 光线感应器，很多Android手机的屏幕亮度是根据这个感应器的数组自动调节的。
5. LINEAR_ACCELERATION 线性加速器，API Level 9新增的。
6. MAGNETIC_FIELD 磁极感应器。
7. ORIENTATION 方向感应器。
8. PRESSURE 压力感应器。
9. PROXIMITY 距离感应器，对于通话后关闭屏幕背光很有用。
10. ROTATION_VECTOR 旋转向量，Android 2.3新增的，如果我们过去处理图像会发现这个还是很有用的，不过这里还是对游戏开发起到辅助。
11. TEMPERATURE 温度感应器，可以获取手机的内部温度，不过和周边的有些差距，毕竟手机内部一般温度比较高。
对于以上感应器eoe提醒开发者，除了特别描述API Level为9或2.3之外的，SDK在1.5即Level3时就已经支持了，不过最终使用还要看手机硬件的支持，很多山寨机或小品牌的设备可能会在这些上面偷工减料，同时eoe提醒大家，感应器的数据刷新比较快一般，考虑到电池功耗一般长时间使用CPU的占用率可能会提升，影响系统性能。
列举手机上已经有的感应器，可以通过SensorManager类的List  getSensorList(int type)获取，返回一个感应器类型的数组。这里在列举时type参数应该写TYPE_ALL