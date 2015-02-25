并不是所有Android手机上都保留有距离感应器。基于这个感应器可以有一些很不错的小应用，比如近距离感应锁屏、解锁……
分享Rexsee的距离感应功能源码，回头可以自个儿做。更多的传感器API我这几天都有陆续发出，大家可以自己搜“Android传感器API之……”，或者去Rexsee的社区找，反正是开源的，http://www.rexsee.com/
SensorProximity.java全部源码：
```  
[Tags]/* 
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
public class RexseeSensorProximity implements JavascriptInterface {
	private static final String INTERFACE_NAME = "Proximity";
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
		return new RexseeSensorProximity(childBrowser);
	}
	public static final String EVENT_ONPROXIMITYCHANGED = "onProximityChanged";
	private final Context mContext;
	private final RexseeBrowser mBrowser;
	private final SensorManager mSensorManager;
	private final SensorEventListener mSensorListener;
	private final Sensor mSensor;
	private int mRate = SensorManager.SENSOR_DELAY_NORMAL;
	private int mCycle = 100; // milliseconds
	private int mEventCycle = 100; // milliseconds
	private float mAccuracy = 0;
	private long lastUpdate = -1;
	private long lastEvent = -1;
	private float value = -999f;
	public RexseeSensorProximity(RexseeBrowser browser) {
		mContext = browser.getContext();
		mBrowser = browser;
		browser.eventList.add(EVENT_ONPROXIMITYCHANGED);
		mSensorManager = (SensorManager) mContext
				.getSystemService(Context.SENSOR_SERVICE);
		mSensor = mSensorManager.getDefaultSensor(Sensor.TYPE_PROXIMITY);
		mSensorListener = new SensorEventListener() {
			@Override
			public void onAccuracyChanged(Sensor sensor, int accuracy) {
			}
			@Override
			public void onSensorChanged(SensorEvent event) {
				if (event.sensor.getType() != Sensor.TYPE_PROXIMITY)
					return;
				long curTime = System.currentTimeMillis();
				if (lastUpdate == -1 || (curTime - lastUpdate) > mCycle) {
					lastUpdate = curTime;
					float lastValue = value;
					value = event.values[SensorManager.DATA_X];
					if (lastEvent == -1 || (curTime - lastEvent) > mEventCycle) {
						if (Math.abs(value - lastValue) > mAccuracy) {
							lastEvent = curTime;
							mBrowser.eventList.run(EVENT_ONPROXIMITYCHANGED);
						}
					}
				}
			}
		};
	}
	public String getLastKnownValue() {
		return (value == -999) ? "null" : String.valueOf(value);
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
	public void setAccuracy(float value) {
		mAccuracy = Math.abs(value);
	}
	public float getAccuracy() {
		return mAccuracy;
	}
	public boolean isReady() {
		return (mSensor == null) ? false : true;
	}
	public void start() {
		if (isReady()) {
			mSensorManager.registerListener(mSensorListener, mSensor, mRate);
		} else {
			mBrowser.exception(getInterfaceName(),
					"Proximity sensor is not found.");
		}
	}
	public void stop() {
		if (isReady()) {
			mSensorManager.unregisterListener(mSensorListener);
		}
	}
}
```