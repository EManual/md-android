效果如下：
![img](P)  
代码如下：
```  
public void onStartTrackingTouch(SeekBar seekBar) {
	if (seekBar.getId() == R.id.seekbar1) {
		textView1.setText("seekbar1开始拖动");
	} else
		textView1.setText("seekbar2开始拖动");
}
@Override
public void onStopTrackingTouch(SeekBar seekBar) {
	if (seekBar.getId() == R.id.seekbar1) {
		textView1.setText("seekbar1停止拖动");
	} else
		textView1.setText("seekbar2停止拖动");
}
```