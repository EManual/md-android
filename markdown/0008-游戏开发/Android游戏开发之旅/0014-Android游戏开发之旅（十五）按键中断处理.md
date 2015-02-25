有关Android平台上游戏开发中我们需要处理一些特别的按键事件，对于突发的事情我们需要特别的考虑，比如突然来电话了和游戏中按下一些特殊的键，比如拍照键
```  
@Override
public boolean dispatchKeyEvent(KeyEvent event)
{
	  switch (event.getKeyCode())
	  {
		case KeyEvent.KEYCODE_VOLUME_UP:  //音量键+
		case KeyEvent.KEYCODE_VOLUME_DOWN:  //音量键-
		case KeyEvent.KEYCODE_CAMERA:  //拍照键
		case KeyEvent.KEYCODE_FOCUS:  //拍照键半按的对焦状态
		//event.getAction() == KeyEvent.ACTION_UP  //提示如果按键按下后弹起时触发
		return true; //这些标记为处理过，则不在往内部传递
		default:
			break;
	}
	return super.dispatchKeyEvent(event);
}
```
对于游戏突然来电话我们一般采取通过PhoneStateListener类提供的public void onCallStateChanged (int state, String incomingNumber) 回调方法可以获取电话的状态，比如常规空闲时CALL_STATE_IDLE、来电时CALL_STATE_RINGING和 CALL_STATE_OFFHOOK 摘机通话中，有关处理的细节网友可以查看Android Git项目中的Music，在Android开源项目中系统自带的音乐播放器可以很好的处理，比如在通话结束后恢复音乐播放，而我们游戏需要做的就是记住当前的游戏状态尽量数据持久化处理，不能因为长时间的通话，游戏的Activity被清理了，这里我们一般通过onSaveInstanceState来保存当前窗口的一些记录，通过Intent标记来让系统管理好我们游戏的生命周期。