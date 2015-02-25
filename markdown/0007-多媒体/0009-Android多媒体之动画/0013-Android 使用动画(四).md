使用动画（4）
(6) 显示的文本可以表示9个位置。要跟踪当前的位置，就需要为每一个位置创建一个enum和一个用来跟踪这个位置的实例变量。
```  
TextPosition textPosition = TextPosition.Center; 
enum TextPosition { 
	UpperLeft, Top, UpperRight, Left, Center, Right, LowerLeft, Bottom, LowerRight
};
```
(7) 创建一个新的方法movePosition，它需要获得当前的位置和要移动的方向，从而计算新的位置。然后，它应该执行一个由第(5)步创建的动画所组成的合适的序列。
```  
private void movePosition(TextPosition _current,
		TextPosition _directionPressed) {
	Animation in;
	Animation out;
	TextPosition newPosition;
	if (_directionPressed == TextPosition.Left) {
		in = slideInLeft;
		out = slideOutLeft;
	} else if (_directionPressed == TextPosition.Right) {
		in = slideInRight;
		out = slideOutRight;
	} else if (_directionPressed == TextPosition.Top) {
		in = slideInTop;
		out = slideOutTop;
	} else {
		in = slideInBottom;
		out = slideOutBottom;
	}
	int newPosValue = _current.ordinal();
	int currentValue = _current.ordinal();
	// 当设备向一个方向移动的时候，要模拟倾斜的效果，应该让文本向着相反
	// 的方向移动
	if (_directionPressed == TextPosition.Bottom)
		newPosValue = currentValue - 3;
	else if (_directionPressed == TextPosition.Top)
		newPosValue = currentValue + 3;
	else if (_directionPressed == TextPosition.Right) {
		if (currentValue % 3 != 0)
			newPosValue = currentValue - 1;
	} else if (_directionPressed == TextPosition.Left) {
		if ((currentValue + 1) % 3 != 0)
			newPosValue = currentValue + 1;
	}
	if (newPosValue != currentValue && newPosValue > -1 && newPosValue < 9) {
		newPosition = TextPosition.values()[newPosValue];
		applyAnimation(in, out, newPosition.toString());
		textPosition = newPosition;
	}
}
```
(7) 创建一个新的方法movePosition，它需要获得当前的位置和要移动的方向，从而计算新的位置。然后，它应该执行一个由第(5)步创建的动画所组成的合适的序列。
```  
@Override
public boolean onKeyDown(int _keyCode, KeyEvent _event) {
	if (super.onKeyDown(_keyCode, _event))
		return true;
	if (_event.getAction() == KeyEvent.ACTION_DOWN) {
		switch (_keyCode) {
		case (KeyEvent.KEYCODE_DPAD_LEFT):
			movePosition(textPosition, TextPosition.Left);
			return true;
		case (KeyEvent.KEYCODE_DPAD_RIGHT):
			movePosition(textPosition, TextPosition.Right);
			return true;
		case (KeyEvent.KEYCODE_DPAD_UP):
			movePosition(textPosition, TextPosition.Top);
			return true;
		case (KeyEvent.KEYCODE_DPAD_DOWN):
			movePosition(textPosition, TextPosition.Bottom);
			return true;
		}
	}
	return false;
}
```
现在运行该应用程序将会出现一个显示"Center"的屏幕。按下4个方向键的任意一个，将会把这个文本滑出屏幕，并显示相应的新位置。
提示：
作为补充，还可以在本例中尝试使用加速传感器，而不是依赖于按下D-pad。
6. 为布局(Layout)和视图集(View Group)添加动画
LayoutAnimation可以用来为View Groups添加动画，并按照预定的顺序，把一个动画(或者动画集合)应用到View Group中的每一个子View中。可以使用LayoutAnimationController来指定一个应用到View Group中的每一个子View中的动画。View Group中包含的每一个View都将应用这个相同的动画，但是可以使用布局动画控制器(Layout Animation Controller)来指定每一个View的顺序和起始时间。
Android包含了两种LayoutAnimationController类。
LayoutAnimationController可以选择每一个View的开始偏移时间(以毫秒为单位)，以及把动画应用到每一个子View的顺序(正向，反向，随机)。
GridLayoutAnimationController是一个派生类，它使用由行和列所映射的网格来向子View分配动画序列。