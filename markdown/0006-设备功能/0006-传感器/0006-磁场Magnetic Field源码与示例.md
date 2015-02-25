Android的磁场传感器，Magnetic Field。。读取磁场的变化，通过该传感器可开发出指南针、罗盘等磁场应用。该传感器读取的数据是空间坐标系三个方向的磁场值，其数据单位为uT，即微特斯拉。
如下是Rexsee针对于磁场传感功能的扩展API源码，基于此可以在其平台上通过JS实现调用。文末补充了一个指南针应用的代码，相当的简单。
```  
/* 
 [Tags]* Copyright (C) 2011 The Rexsee Open Source Project 
 [Tags]* 
 [Tags]* Licensed under the Rexsee License, Version 1.0 (the "License"); 
 [Tags]* you may not use this file except in compliance with the License. 
 [Tags]* You may obtain a copy of the License at 
 [Tags]* 
 [Tags]*      http://www.rexsee.com/CN/legal/license.html 
 [Tags]* 
 [Tags]* Unless required by applicable law or agreed to in writing, software 
 [Tags]* distributed under the License is distributed on an "AS IS" BASIS, 
 [Tags]* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 [Tags]* See the License for the specific language governing permissions and 
 [Tags]* limitations under the License. 
 [Tags]*/
import android.content.Context;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
public class RexseeSensorMagneticField implements JavascriptInterface {
	private static final String INTERFACE_NAME = "MagneticField";
	@Override
	public String getInterfaceName() {
		return mBrowser.application.resources.prefix + INTERFACE_NAME;
	}
	@Override
	public JavascriptInterface getInheritInterface(RexseeBrowser childBrowser) {
		return this;
	}
	@Override
	public JavascriptInterface getNewInterface(RexseeBrowser childBrowser) {
		return new RexseeSensorMagneticField(childBrowser);
	}
	public static final String EVENT_ONMAGNETICFIELDCHANGED = "onMagneticFieldChanged";
	private final Context mContext;
	private final RexseeBrowser mBrowser;
	private final SensorManager mSensorManager;
	private final SensorEventListener mSensorListener;
	private final Sensor mSensor;
	private int mRate = SensorManager.SENSOR_DELAY_NORMAL;
	private int mCycle = 100; // milliseconds
	private int mEventCycle = 100; // milliseconds
	private float mAccuracyX = 0;
	private float mAccuracyY = 0;
	private float mAccuracyZ = 0;
	private long lastUpdate = -1;
	private long lastEvent = -1;
	private float x = -999f;
	private float y = -999f;
	private float z = -999f;
	public RexseeSensorMagneticField(RexseeBrowser browser) {
		mContext = browser.getContext();
		mBrowser = browser;
		browser.eventList.add(EVENT_ONMAGNETICFIELDCHANGED);
		mSensorManager = (SensorManager) mContext
				.getSystemService(Context.SENSOR_SERVICE);
		mSensor = mSensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD);
		mSensorListener = new SensorEventListener() {
			@Override
			public void onAccuracyChanged(Sensor sensor, int accuracy) {
			}
			@Override
			public void onSensorChanged(SensorEvent event) {
				if (event.sensor.getType() != Sensor.TYPE_MAGNETIC_FIELD)
					return;
				long curTime = System.currentTimeMillis();
				if (lastUpdate == -1 || (curTime - lastUpdate) > mCycle) {
					lastUpdate = curTime;
					float lastX = x;
					float lastY = y;
					float lastZ = z;
					x = event.values[SensorManager.DATA_X];
					y = event.values[SensorManager.DATA_Y];
					z = event.values[SensorManager.DATA_Z];
					if (lastEvent == -1 || (curTime - lastEvent) > mEventCycle) {
						if ((mAccuracyX >= 0 && Math.abs(x - lastX) > mAccuracyX)
								|| (mAccuracyY >= 0 && Math.abs(y - lastY) > mAccuracyY)
								|| (mAccuracyZ >= 0 && Math.abs(z - lastZ) > mAccuracyZ)) {
							lastEvent = curTime;
							mBrowser.eventList
									.run(EVENT_ONMAGNETICFIELDCHANGED);
						}
					}
				}
			}
		};
	}
	public String getLastKnownX() {
		return (x == -999) ? "null" : String.valueOf(x);
	}
	public String getLastKnownY() {
		return (y == -999) ? "null" : String.valueOf(y);
	}
	public String getLastKnownZ() {
		return (z == -999) ? "null" : String.valueOf(z);
	}
	public void setRate(String rate) {
		mRate = SensorRate.getInt(rate);
	}
	public String getRate() {
		return SensorRate.getString(mRate);
	}
	public void setCycle(int milliseconds) {
		mCycle = milliseconds;
	}
	public int getCycle() {
		return mCycle;
	}
	public void setEventCycle(int milliseconds) {
		mEventCycle = milliseconds;
	}
	public int getEventCycle() {
		return mEventCycle;
	}
	public void setAccuracy(float x, float y, float z) {
		mAccuracyX = x;
		mAccuracyY = y;
		mAccuracyZ = z;
	}
	public float getAccuracyX() {
		return mAccuracyX;
	}
	public float getAccuracyY() {
		return mAccuracyY;
	}
	public float getAccuracyZ() {
		return mAccuracyZ;
	}
	public boolean isReady() {
		return (mSensor == null) ? false : true;
	}
	public void start() {
		if (isReady()) {
			mSensorManager.registerListener(mSensorListener, mSensor, mRate);
		} else {
			mBrowser.exception(getInterfaceName(),
					"Magnetic-field sensor is not found.");
		}
	}
	public void stop() {
		if (isReady()) {
			mSensorManager.unregisterListener(mSensorListener);
		}
	}
}
```
在Rexsee的社区找了个指南针实例开发的帖子，原文供参考：http://www.rexsee.com/CN/bbs/thread/2011-10-09/78.html
先找罗盘图片；打开传感器rexseeOrientation.start()；把方向改变时触发的事件写上：然后是处理图片。搞定~~
```  
function onOrientationChanged(){ //方向传感器事件,即当方向发生改变时触发的动作
	var x = rexseeOrientation.getLastKnownX();
	x = 90 - x;
	document.getElementById('oriDiv').style.webkitTransform = 'rotate('+x+"deg)";
}
```