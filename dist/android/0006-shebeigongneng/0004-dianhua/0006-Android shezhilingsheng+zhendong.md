有时候一些通讯软件需要这些个功能，比如说收到短信，通知等，要求手机发出铃声，或震动，或发光以提示用户知晓。往往手机都是有默认设置的，比如说用户开启了铃声+震动；只铃声不震动；完全静音等等...
这个时候就需要有一个规则了，起码软件的设置不能跟系统的冲突吧，中间的一些逻辑是要处理好的！之前做过的软件中有这么个需求，而且代码是我负责的，所以总结一下。
思路：
1. 软件应该有个自己的设置配置文件，用以保存，自己的软件的提醒规则。
2. 遵从系统的设置，比如说：系统是完全静音的，人家想睡觉啦，你软件虽然是铃声震动全开，也得乖乖闭嘴。
3. 如果有需要提醒了，先获取系统的配置，然后做逻辑判断给予什么样的提醒。
```  
// 首先需要接收一个Notification的参数
private void setAlarmParams(Notification notification) {
	// AudioManager provides access to volume and ringer mode control.
	// AudioManager volMgr = (AudioManager)
	// mAppContext.getSystemService(Context.AUDIO_SERVICE);
	switch (volMgr.getRingerMode()) {
	// 获取系统设置的铃声模式
	case AudioManager.RINGER_MODE_SILENT:
		// 静音模式，值为0，这时候不震动，不响铃
		notification.sound = null;
		notification.vibrate = null;
		break;
	case AudioManager.RINGER_MODE_VIBRATE:
		// 震动模式，值为1，这时候震动，不响铃
		notification.sound = null;
		notification.defaults |= Notification.DEFAULT_VIBRATE;
		break;
	case AudioManager.RINGER_MODE_NORMAL:
		// 常规模式，值为2，分两种情况：1_响铃但不震动，2_响铃+震动
		Uri ringTone = null;
		// 获取软件的设置
		SharedPreferences sp = PreferenceManager
				.getDefaultSharedPreferences(mAppContext);
		if (!sp.contains(SystemUtil.KEY_RING_TONE)) {
			// 如果没有生成配置文件，那么既有铃声又有震动
			notification.defaults |= Notification.DEFAULT_VIBRATE;
			notification.defaults |= Notification.DEFAULT_SOUND;
		} else {
			String ringFile = sp.getString(SystemUtil.KEY_RING_TONE, null);
			if (ringFile == null) {
				// 无值，为空，不播放铃声
				ringTone = null;
			} else if (!TextUtils.isEmpty(ringFile)) {
				// 有铃声：1，默认2自定义，都返回一个uri
				ringTone = Uri.parse(ringFile);
			}
			notification.sound = ringTone;
			boolean vibrate = sp.getBoolean(
					SystemUtil.KEY_NEW_MAIL_VIBRATE, true);
			if (vibrate == false) {
				// 如果软件设置不震动，那么就不震动了
				notification.vibrate = null;
			} else {
				// 否则就是需要震动，这时候要看系统是怎么设置的：不震动=0;震动=1；仅在静音模式下震动=2；
				if (volMgr
						.getVibrateSetting(AudioManager.VIBRATE_TYPE_RINGER) == AudioManager.VIBRATE_SETTING_OFF) {
					// 不震动
					notification.vibrate = null;
				} else if (volMgr
						.getVibrateSetting(AudioManager.VIBRATE_TYPE_RINGER) == AudioManager.VIBRATE_SETTING_ONLY_SILENT) {
					// 只在静音时震动
					notification.vibrate = null;
				} else {
					// 震动
					notification.defaults |= Notification.DEFAULT_VIBRATE;
				}
			}
		}
		notification.flags |= Notification.FLAG_SHOW_LIGHTS;
		// 都给开灯
		break;
	default:
		break;
	}
}
```
具体的实现就如代码那样子了，注释也很清楚了，其中SharedPreferences sp = PreferenceManager.getDefaultSharedPreferences(mAppContext);
这个不多做解释，就是获取软件的配置信息。