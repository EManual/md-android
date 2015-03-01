个人觉得温度传感器的应用频率应该不会太高，这也是部分Android手机直接将这个功能屏蔽掉的原因所在。不过从创意角度出发，基于这个传感器可以开发出一些有意思的应用，比如手机温度计、室温监测等等。
个性化的应用就不说了，具体的说实话我也没做，只是把Rexsee的功能源码分享出来，回头要是有应用了再分享。和之前的帖子里提到的一样，Android平台带有大量的传感器功能，相关的原生源码可以在Rexsee的开源社区找到：http://www.rexsee.com/
SensorTemperature功能源码如下：
```  
 /* 
  * Copyright (C) 2011 The Rexsee Open Source Project 
  * 
  * Licensed under the Rexsee License, Version 1.0 (the "License"); 
  * you may not use this file except in compliance with the License. 
  * You may obtain a copy of the License at 
  * 
  *      http://www.rexsee.com/CN/legal/license.html 
  * 
  * Unless required by applicable law or agreed to in writing, software 
  * distributed under the License is distributed on an "AS IS" BASIS, 
  * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
  * See the License for the specific language governing permissions and 
  * limitations under the License. 
  */
import android.content.Context;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
public class RexseeSensorTemperature implements JavascriptInterface {
	private static final String INTERFACE_NAME = "Temperature";
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
		return new RexseeSensorTemperature(childBrowser);
	}
	public static final String EVENT_ONTEMPERATURECHANGED = "onTemperatureChanged";
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
	public RexseeSensorTemperature(RexseeBrowser browser) {
		mContext = browser.getContext();
		mBrowser = browser;
		browser.eventList.add(EVENT_ONTEMPERATURECHANGED);
		mSensorManager = (SensorManager) mContext
				.getSystemService(Context.SENSOR_SERVICE);
		mSensor = mSensorManager.getDefaultSensor(Sensor.TYPE_TEMPERATURE);
		mSensorListener = new SensorEventListener() {
			@Override
			public void onAccuracyChanged(Sensor sensor, int accuracy) {
			}
			@Override
			public void onSensorChanged(SensorEvent event) {
				if (event.sensor.getType() != Sensor.TYPE_TEMPERATURE)
					return;
				long curTime = System.currentTimeMillis();
				if (lastUpdate == -1 || (curTime - lastUpdate) > mCycle) {
					lastUpdate = curTime;
					float lastValue = value;
					value = event.values[SensorManager.DATA_X];
					if (lastEvent == -1 || (curTime - lastEvent) > mEventCycle) {
						if (Math.abs(value - lastValue) > mAccuracy) {
							lastEvent = curTime;
							mBrowser.eventList.run(EVENT_ONTEMPERATURECHANGED);
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
					"Temperature sensor is not found.");
		}
	}
	public void stop() {
		if (isReady()) {
			mSensorManager.unregisterListener(mSensorListener);
		}
	}
}
```