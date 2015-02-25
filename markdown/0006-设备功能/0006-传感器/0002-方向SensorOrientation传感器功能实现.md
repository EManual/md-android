继续明确一下空间坐标系的三个方向：
x 方向就是手机的水平方向，右为正；
y 方向就是手机的水平垂直方向，前为正；
z 方向就是手机的空间垂直方向，天空的方向为正，地球的方向为负。
方向角的定义是手机y轴 水平面上的投影 与 正北方向的夹角。 （值得范围是 0 ~ 359 其中0=North, 90=East, 180=South, 270=West）
倾斜角的定义是手机y轴 与水平面的夹角 （手机z轴向y轴方向移动为正 ,值得范围是 -180 ~ 180）
旋转角的定义是手机x轴 与水平面的夹角 （手机x轴离开z轴方向为正， 值得范围是 -90 ~ 90）
也就是说，当你把手机水平放置在桌面上(屏幕向上)且手机指向正北(Y轴方向)，此时传感器获得的xyz三个值应该都为0。
如下是Rexsee实现的方向传感器功能源码。我会把Rexsee扩展的全部传感器源码都陆续贴出来，感兴趣的也可以直接去Rexsee社区查阅，反正都是开源的：http://www.rexsee.com。
但是首先需要知道，并不是所有的Android手机都支持方向传感器。。比如说乐Phone，好像就是加速度、重力和光线，方向和磁场貌似都不支持。
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
import rexsee.core.browser.JavascriptInterface;
import rexsee.core.browser.RexseeBrowser;
import android.content.Context;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
public class RexseeSensorOrientation implements JavascriptInterface {
	public static final String INTERFACE_NAME = "Orientation";
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
		return new RexseeSensorOrientation(childBrowser);
	}
	public static final String EVENT_ONORIENTATIONCHANGED = "onOrientationChanged";
	private final Context mContext;
	private final RexseeBrowser mBrowser;
	private final SensorManager mSensorManager;
	private SensorEventListener mSensorListener;
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
	public RexseeSensorOrientation(RexseeBrowser browser) {
		mContext = browser.getContext();
		mBrowser = browser;
		browser.eventList.add(EVENT_ONORIENTATIONCHANGED);
		mSensorManager = (SensorManager) mContext
				.getSystemService(Context.SENSOR_SERVICE);
		mSensor = mSensorManager.getDefaultSensor(Sensor.TYPE_ORIENTATION);
		mSensorListener = new SensorEventListener() {
			@Override
			public void onAccuracyChanged(Sensor sensor, int accuracy) {
			}
			@Override
			public void onSensorChanged(SensorEvent event) {
				if (event.sensor.getType() != Sensor.TYPE_ORIENTATION)
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
							mBrowser.eventList.run(EVENT_ONORIENTATIONCHANGED);
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
					"Orientation sensor is not found.");
		}
	}
	public void stop() {
		if (isReady()) {
			mSensorManager.unregisterListener(mSensorListener);
		}
	}
}
```