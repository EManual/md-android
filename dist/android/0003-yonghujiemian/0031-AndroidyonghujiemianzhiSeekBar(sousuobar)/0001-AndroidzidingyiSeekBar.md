我们还是来看看一个很炫的效果图吧：
![img](P)  
我们想这样怎么实现那，我们还是看看代码吧
```  
<SeekBar
    android:id="@+id/seekbar"
    style="?android:attr/progressBarStyleHorizontal"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginLeft="10dip"
    android:layout_marginRight="10dip"
    android:layout_weight="1"
    android:paddingLeft="5dip"
    android:paddingRight="5dip"
    android:progressDrawable="@layout/seekbar_style"
    android:thumb="@layout/thumb" />
```
上面的代码主要就是说了这么样设置seekbar。这么些代码其中我们最主要的就是这两句代码，
```  
android:progressDrawable="@layout/seekbar_style"     
android:thumb="@layout/thumb
```
大家可千万要注意呀。
怎么去定义呢？看下面的代码：seekbar_style.xml让我们知道怎么在xml里设置代码。
```  
<?xml version="1.0" encoding="UTF-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item android:id="@android:id/background">
        <shape>
            <corners android:radius="10dip" />
            <gradient
                android:angle="270"
                android:centerColor="ff000000"
                android:centerY="0.45"
                android:endColor="ff808A87"
                android:startColor="ffffffff" />
        </shape>
    </item>
    <item android:id="@android:id/progress">
        <clip>
            <shape>
                <corners android:radius="10dip" />
                <gradient
                    android:angle="270"
                    android:centerColor="ffFFFF00"
                    android:centerY="0.45"
                    android:endColor="ffAABD00"
                    android:startColor="ffffffff" />
            </shape>
        </clip>
    </item>
</layer-list>
```
thumb.xml的代码，这里就是哪个条上的进度按钮，你可以设置不图片。方形，圆形都可以。
```  
<?xml version="1.0" encoding="UTF-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- 按下状态 -->
    <item android:drawable="@drawable/thumb_normal" android:state_pressed="true"/>
    <!-- 普通无焦点状态 -->
    <item android:drawable="@drawable/thumb_normal" android:state_focused="false" android:state_pressed="false"/>
</selector>
```
Java代码的处理：并实现播放中的拖动功能，这可是全不代码的核心哦，大家可要认真看看哦。
```  
seekBar = (SeekBar) controlView.findViewById(R.id.seekbar);
seekBar.setOnSeekBarChangeListener(new OnSeekBarChangeListener() {
	@Override
	public void onProgressChanged(SeekBar seekbar, int progress,
			boolean fromUser) {
		if (fromUser) {
			// if(!isOnline){
			vv.seekTo(progress);
			// }
		}
	}
	@Override
	public void onStartTrackingTouch(SeekBar arg0) {
		myHandler.removeMessages(HIDE_CONTROLER);
	}
	@Override
	public void onStopTrackingTouch(SeekBar seekBar) {
		myHandler.sendEmptyMessageDelayed(HIDE_CONTROLER, TIME);
	}
});
```