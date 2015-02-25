很多音乐播放器界面上都有一个音量seekbar，那么在android里面是如何实现的呢？
首先分析下要解决的问题：
1.获取媒体播放的音量。
2.通过seekbar可以增减音量
3.用户按下音量键增减音量，seekbar保持同步
对于第一个问题：Android系统提供AudioManager类来获得系统audio服务。
对于第二个问题：实现seekBarChangeLIstener里面onProgressChanged方法。
对于第三个问题：用线程来同步更新UI。
设置音量的代码：
```  
private void setVolum() {
	maxVolume = mAudioManager.getStreamMaxVolume(AudioManager.STREAM_MUSIC);
	volSeekBar.setMax(maxVolume);
	currentVolume = mAudioManager
			.getStreamVolume(AudioManager.STREAM_MUSIC);
	volSeekBar.setProgress(currentVolume);
	mVolume.setText(currentVolume * 100 / maxVolume + " ");
}
```
实现seekbar控制音量代码：
```  
OnSeekBarChangeListener seekBarChangeListener = new OnSeekBarChangeListener() {
	public void onProgressChanged(SeekBar seekBar, int progress,
			boolean fromUser) {
		mAudioManager.setStreamVolume(AudioManager.STREAM_MUSIC, progress, 0);
	}
	public void onStartTrackingTouch(SeekBar seekBar) {
	}
	public void onStopTrackingTouch(SeekBar seekBar) {
	}
};
```
线程更新UI代码：
```  
Handler myHandler = new Handler() {
	public void handleMessage(Message msg) {
		switch (msg.what) {
		case PROGRESS_CHANGED:
			setVolum();
			/* sendEmptyMessageDelayed(PROGRESS_CHANGED, 200); */
			break;
		}
	}
};
class myVolThread implements Runnable {
	public void run() {
		while (!Thread.currentThread().isInterrupted()) {
			Message message = new Message();
			message.what = PROGRESS_CHANGED;
			MainActivity.this.myHandler.sendMessage(message);
			try {
				Thread.sleep(100);
			} catch (InterruptedException e) {
				Thread.currentThread().interrupt();
			}
		}
	}
}
```