众所周知，Toast只能显示很短的时间后就自动消失，可是如果我们需要像Toast的效果一样提示一些信息，但是我们又想让它接受用户事件，比如：显示一个图片信息，用户点击图片后消失，还如何实现呢？            
我们完全可以用一个dialog来实现：
```  
private Dialog alert;
ImageView v = new ImageView(this);
v.setImageBitmap(BitmapManager.toastBitmap[level]); // 创建一个imageview
alert = new Dialog(this, R.style.dialog); // 在values下创建一个styles.xml文件，代码见后
alert.setCancelable(false);
alert.setContentView(v);// 将imageview加入dialog
alert.show();
v.setOnClickListener(new OnClickListener() {
	public void onClick(View v) {
		GameView.this.alert.cancel();
	}
});
```
这样就会显示一个带图片的dialog，并且点击图片之后会消失。
styles.xml代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="dialog" parent="@android:style/Theme.Dialog">
        <item name="android:windowFrame">@null</item>
        <item name="android:windowIsFloating">true</item>
        <item name="android:windowIsTranslucent">false</item>
        <item name="android:windowNoTitle">true</item>
        <item name="android:background">@android:color/black</item>
        <item name="android:windowBackground">@null</item>
        <item name="android:backgroundDimEnabled">false</item>
    </style>
</resources>
```