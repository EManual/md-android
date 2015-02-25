今天我们来讲一讲怎么样监听Android音量键，其实这个把是一个很简单的例子，有的人都不爱去写这么个小例子。但是还是有一些新手不是怎么会去监听Android音量键，那么有不会的就得有人写出来给大家分享，就算是很简单的例子也要写出来，童鞋们可要来看看哦。老样子我们还是来看看代码吧：
```  
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
	if (keyCode == KeyEvent.KEYCODE_VOLUME_DOWN) {
		myScrollBy(200);
		return true;
	} else if (keyCode == KeyEvent.KEYCODE_VOLUME_UP) {
		myScrollBy(-200);
		return true;
	} else {
		return super.onKeyDown(keyCode, event);
	}
}
```
大家看了一后会说，就这么个简单的例子呀，但是为了一些android新入学的，我还是写了一下。让这些童鞋少走一些弯路。不要想我们以前，摸索的前进。看过的童鞋帮顶呀。