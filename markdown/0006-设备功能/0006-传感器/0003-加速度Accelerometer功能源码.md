加速度传感器，主要是感应手机的运动。捕获三个参数，分别表示空间坐标系中X、Y、Z轴方向上的加速度减去重力加速度在相应轴上的分量，其单位均为m/s2。和之前几篇传感器功能介绍中的一样，这几个方向就不再多说了~~
Rexsee的源码如下，有兴趣的朋友可以再去找找，社区公开的源码里有包括加速度在内的，重力、磁场、温度、距离和方向等传感器功能。http://www.rexsee.com/CN/helpReference.php
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
public class RexseeSensorAccelerometer implements JavascriptInterface {
	private static final String INTERFACE_NAME = "Accelerometer";
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
		return new RexseeSensorAccelerometer(childBrowser);
	}
	public static final String EVENT_ONACCELERATE = "onAccelerometerChanged";
	private final Context mContext;
	private final RexseeBrowser mBrowser;
	private final SensorManager mSensorManager;
	private final SensorEventListener mSensorListener;
	private final Sensor mSensor;
	private int mRate = SensorManager.SENSOR_DELAY_NORMAL;
	private int mCycle = 100;
	private int mEventCycle = 100;
	private float mAccuracyX = 0;
	private float mAccuracyY = 0;
	private float mAccuracyZ = 0;
	private long lastUpdate = -1;
	private long lastEvent = -1;
	private float x = -999, y = -999, z = -999;
	public RexseeSensorAccelerometer(RexseeBrowser browser) {
		mContext = browser.getContext();
		mBrowser = browser;
		browser.eventList.add(EVENT_ONACCELERATE);
		mSensorManager = (SensorManager) mContext
				.getSystemService(Context.SENSOR_SERVICE);
		mSensor = mSensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
		mSensorListener = new SensorEventListener() {
			@Override
			public void onAccuracyChanged(Sensor sensor, int accuracy) {
			}
			@Override
			public void onSensorChanged(SensorEvent event) {
				if (event.sensor.getType() != Sensor.TYPE_ACCELEROMETER)
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
							mBrowser.eventList.run(EVENT_ONACCELERATE);
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
					"Accelerometer is not found.");
		}
	}
	public void stop() {
		if (isReady()) {
			mSensorManager.unregisterListener(mSensorListener);
		}
	}
}
```