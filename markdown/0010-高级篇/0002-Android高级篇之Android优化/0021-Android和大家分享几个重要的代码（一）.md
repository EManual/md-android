####  1，设置静音和振动
静音和振动都属于来电后的动作.所以在设置静音和振动时都只是设置一些标识，并往数据库写入相应标识。
文件：packages/apps/settings/src/com/android/settings/SoundAndDisplaySettings.java
```  
private CheckBoxPreference mSilent;
private CheckBoxPreference mVibrate;
private void setRingerMode(boolean silent, boolean vibrate) {
	if (silent) {
		mAudioManager
				.setRingerMode(vibrate ? AudioManager.RINGER_MODE_VIBRATE
						: AudioManager.RINGER_MODE_SILENT);
	} else {
		mAudioManager.setRingerMode(AudioManager.RINGER_MODE_NORMAL);
		mAudioManager.setVibrateSetting(AudioManager.VIBRATE_TYPE_RINGER,
				vibrate ? AudioManager.VIBRATE_SETTING_ON
						: AudioManager.VIBRATE_SETTING_OFF);
	}
}
public boolean onPreferenceTreeClick(PreferenceScreen preferenceScreen,
		Preference preference) {
	if (preference == mSilent || preference == mVibrate) {
		setRingerMode(mSilent.isChecked(), mVibrate.isChecked());
		if (preference == mSilent)
			updateState(false);
	}
}
```
静音和振动是复选框按钮，两个中有一个发生变化时调用setRingerMode对状态进行设置；如下状态描术：
RINGER_MODE_SILENT 静音，且无振动。
RINGER_MODE_VIBRATE 静音，但有振动。
RINGER_MODE_NORMAL 正常声音，振动开关由setVibrateSetting决定。
铃响模式的设置是通过mAudioManager（音频管理器）来实现的。
#### 2 音频管理器服务
mAudioManager所在服务如下：
文件：frameworks/base/media/java/android/media/AudioManager.java
```  
public static final int RINGER_MODE_SILENT = 0;
public static final int RINGER_MODE_VIBRATE = 1;
public static final int RINGER_MODE_NORMAL = 2;
public void setRingerMode(int ringerMode) {
	IAudioService service = getService();
	try {
		service.setRingerMode(ringerMode);
	} catch (RemoteException e) {
		Log.e(TAG, "Dead object in setRingerMode", e);
	}
}
```
将铃响模式值传给音频接口服务IaudioService
```  
public static final int VIBRATE_TYPE_RINGER = 0;
public static final int VIBRATE_TYPE_NOTIFICATION = 1;
public static final int VIBRATE_SETTING_OFF = 0;
public static final int VIBRATE_SETTING_ON = 1;
public static final int VIBRATE_SETTING_ONLY_SILENT = 2;
public void setVibrateSetting(int vibrateTyp, int vibrateSetting) {
	IAudioService service = getService();
	try {
		service.setVibrateSetting(vibrateType, vibrateSetting);
	} catch (RemoteException e) {
		Log.e(TAG, "Dead object in setVibrateSetting", e);
	}
}
```
将振动类型和振动设置传给音频接口服务IaudioService,IaudioService的定义如下:
frameworks/base/media/java/android/media/IAudioService.aidl
frameworks/base/media/java/android/media/AudioService.java
文件: frameworks/base/media/java/android/media/AudioService.java
文件: frameworks/base/core/java/android/provider/Settings.java
```  
public void setRingerMode(int ringerMode) {
	synchronized (mSettingsLock) {
		if (ringerMode != mRingerMode) {
			setRingerModeInt(ringerMode, true);
			// Send sticky broadcast
			broadcastRingerMode();
		}
	}
}
```
将对应模式下的音量写入数据库,并将该模式广播。
```  
public void setVibrateSetting(int vibrateType, int vibrateSetting) {
	mVibrateSetting = getValueForVibrateSetting(mVibrateSetting,
			vibrateType, vibrateSetting);
	// Broadcast change
	broadcastVibrateSetting(vibrateType);
	// Post message to set ringer mode (it in turn will post a message
	// to persist)
	sendMsg(mAudioHandler, MSG_PERSIST_VIBRATE_SETTING, SHARED_MSG,
			SENDMSG_NOOP, 0, 0, null, 0);
}
```
同样将振动模式写入数据库，并广播该模式。
#### 3 硬件服务
文件：frameworks/base/services/java/com/android/server/HardwareService.java
开始振动：
```  
public void vibrate(long milliseconds, IBinder token) {
	if (mContext
			.checkCallingOrSelfPermission(android.Manifest.permission.VIBRATE) != PackageManager.PERMISSION_GRANTED) {
		throw new SecurityException("Requires VIBRATE permission");
	}
	// We're running in the system server so we cannot crash. Check for a
	// timeout of 0 or negative. This will ensure that a vibration has
	// either a timeout of > 0 or a non-null pattern.
	if (milliseconds <= 0
			|| (mCurrentVibration != null && mCurrentVibration
					.hasLongerTimeout(milliseconds))) {
		// Ignore this vibration since the current vibration will play for
		// longer than milliseconds.
		return;
	}
	Vibration vib = new Vibration(token, milliseconds);
	synchronized (mVibrations) {
		removeVibrationLocked(token);
		doCancelVibrateLocked();
		mCurrentVibration = vib;
		startVibrationLocked(vib);
	}
}
private void startVibrationLocked(final Vibration vib) {
	if (vib.mTimeout != 0) {
		vibratorOn(vib.mTimeout);
		mH.postDelayed(mVibrationRunnable, vib.mTimeout);
	} else {
		// mThread better be null here. doCancelVibrate should always be
		// called before startNextVibrationLocked or startVibrationLocked.
		mThread = new VibrateThread(vib);
		mThread.start();
	}
}
```
该接口允许设置振动的时间长度，通过调用vibratorOn(vib.mTimeout);实现对底层硬件的操作。
取消振动：
```  
public void cancelVibrate(IBinder token) {
	mContext.enforceCallingOrSelfPermission(
			android.Manifest.permission.VIBRATE, "cancelVibrate");
	// so wakelock calls will succeed
	long identity = Binder.clearCallingIdentity();
	try {
		synchronized (mVibrations) {
			final Vibration vib = removeVibrationLocked(token);
			if (vib == mCurrentVibration) {
				doCancelVibrateLocked();
				startNextVibrationLocked();
			}
		}
	} finally {
		Binder.restoreCallingIdentity(identity);
	}
}
private void doCancelVibrateLocked() {
	if (mThread != null) {
		synchronized (mThread) {
			mThread.mDone = true;
			mThread.notify();
		}
		mThread = null;
	}
	vibratorOff();
	mH.removeCallbacks(mVibrationRunnable);
}
```