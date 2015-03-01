ViewFlipper 
[功能] 
ViewFlipper 可以包含多个View且View之间的切换有Animation。
1、创建包含ViewFlipper的main.xml还包含2个Button 用于各个View切换 。
```              
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >
        <Button
            android:id="@+id/previousButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Previous" />
        <Button
            android:id="@+id/nextButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Next" />
    </LinearLayout>
    <ViewFlipper
        android:id="@+id/flipper"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:gravity="center" >
    </ViewFlipper>
</LinearLayout>
```
2. 设定 Animation 效果 
```  
flipper = (ViewFlipper) findViewById(R.id.flipper); 
flipper.setInAnimation(AnimationUtils.loadAnimation(this, android.R.anim.fade_in)); 
flipper.setOutAnimation(AnimationUtils.loadAnimation(this, android.R.anim.fade_out)); 
```
3. 在 ViewFlipper 里面增加各种 View 
```  
flipper.addView(addTextByText("HelloAndroid"));
flipper.addView(addImageById(R.drawable.beijing_003_mb5ucom));
flipper.addView(addTextByText("eoe.Android"));
flipper.addView(addImageById(R.drawable.beijing_004_mb5ucom));
flipper.addView(addTextByText("Gryphone"));
public View addTextByText(String text) {
	TextView tv = new TextView(this);
	tv.setText(text);
	tv.setGravity(1);
	return tv;
}
public View addImageById(int id) {
	ImageView iv = new ImageView(this);
	iv.setImageResource(id);
	return iv;
}
```
4. View 切换
```  
flipper.showNext( ); //下一个View
flipper.showPrevious( ); //上一个View 
```