在本示例中，我们使用第二种方案：
```  
@Override
public boolean onTouchEvent(MotionEvent touchevent) {
	switch (touchevent.getAction()) {
	case MotionEvent.ACTION_DOWN: {
		oldTouchValue = touchevent.getX();
		break;
	}
	case MotionEvent.ACTION_UP: {
		if (this.searchOk == false)
			return false;
		float currentX = touchevent.getX();
		if (oldTouchValue < currentX) {
			vf.setInAnimation(AnimationHelper.inFromLeftAnimation());
			vf.setOutAnimation(AnimationHelper.outToRightAnimation());
			vf.showNext();
		}
		if (oldTouchValue > currentX) {
			vf.setInAnimation(AnimationHelper.inFromRightAnimation());
			vf.setOutAnimation(AnimationHelper.outToLeftAnimation());
			vf.showPrevious();
		}
		break;
	}
	}
	return false;
}
```
正如你看到的，当用户第一次触摸设备时，一个浮点型变量(oldTouchValue)将读入用户手指位置的X坐标，然后当用户释放手指时，检查事件的新位置决定用户是向前还是向后(oldTouchValue<currentX，oldTouchValue > currentX)。
要改变布局的代码是相同的，但我使用不同的动画来表示用户想向前还是向后，关于动画和 AnimationHelper的详细解释参见代码：
```  
// 对于向前的运动
public static Animation inFromRightAnimation() {
	Animation inFromRight = new TranslateAnimation(
			Animation.RELATIVE_TO_PARENT, +1.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f);
	inFromRight.setDuration(350);
	inFromRight.setInterpolator(new AccelerateInterpolator());
	return inFromRight;
}
public static Animation outToLeftAnimation() {
	Animation outtoLeft = new TranslateAnimation(
			Animation.RELATIVE_TO_PARENT, 0.0f,
			Animation.RELATIVE_TO_PARENT, -1.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f);
	outtoLeft.setDuration(350);
	outtoLeft.setInterpolator(new AccelerateInterpolator());
	return outtoLeft;
}
// 对于向后的运动
public static Animation inFromLeftAnimation() {
	Animation inFromLeft = new TranslateAnimation(
			Animation.RELATIVE_TO_PARENT, -1.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f);
	inFromLeft.setDuration(350);
	inFromLeft.setInterpolator(new AccelerateInterpolator());
	return inFromLeft;
}
public static Animation outToRightAnimation() {
	Animation outtoRight = new TranslateAnimation(
			Animation.RELATIVE_TO_PARENT, 0.0f,
			Animation.RELATIVE_TO_PARENT, +1.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f,
			Animation.RELATIVE_TO_PARENT, 0.0f);
	outtoRight.setDuration(350);
	outtoRight.setInterpolator(new AccelerateInterpolator());
	return outtoRight;
}
```