图片放大的思路：
第一、可以通过Matrix对象来变换图像，在选择的时候放大，在失去焦点的时候，缩小到原来的大小。
```  
double scale = 1.2;
int width = bm.getWidth();
int height = bm.getHeight();
Log.i("size:", width+"");
float scaleWidth = (float)(scale*width);
float scaleHeight = (float)(scale*height);
Log.i("size:", scaleWidth+"");
Matrix matrix = new Matrix();
matrix.postScale(scaleWidth, scaleHeight);
bm = Bitmap.createBitmap(bm, 0, 0, width, height, matrix, true);
```
第二 、通过动画
```  
<?xml version="1.0 encoding="utf-8"?>
<scale xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="500"
    android:fromXScale="1"
    android:fromYScale="1"
    android:interpolator="@android:anim/decelerate_interpolator"
    android:pivotX="50%"
    android:pivotY="50%"
    android:toXScale="1.1"
    android:toYScale="1.1" >
</scale>
```
第三、通过setLayoutParams
```  
view.setLayoutParams(new Gallery.LayoutParams(150, 150));
int mCounts = g.getCount() - 1;
if (position > 0 && (position < mCounts)) {
	g.getChildAt(position - 1).setLayoutParams(
			new Gallery.LayoutParams(136, 88));
	g.getChildAt(position + 1).setLayoutParams(
			new Gallery.LayoutParams(136, 88));
}
if (position == 0) {
	g.getChildAt(position + 1).setLayoutParams(
			new Gallery.LayoutParams(136, 88));
}
if (position == mCounts) {
	g.getChildAt(position - 1).setLayoutParams(
			new Gallery.LayoutParams(136, 88));
}
```
注释：其中(136,88)是gallery中图片的大小，是在ImageAdapter里面设置的。(150,150)是选中图片放大后的大小，可以随便设置，只要跟(136,88)区别就行了，是为了观察变化，我设置的是150而已。
第四 、通过动画和LayoutParam结合
```  
gallery.setOnItemSelectedListener(new OnItemSelectedListener() {
	@Override
	public void onItemSelected(AdapterView<?> arg0, View arg1,
	int arg2, long arg3) {
		ImageView v = (ImageView) arg1;
		if (tempView != null && v.hashCode() != tempView.hashCode()) {
			tempView.setLayoutParams(new Gallery.LayoutParams(50, 50));
		}
		v.startAnimation(toLarge);
		tempView = v;
		v.setLayoutParams(new Gallery.LayoutParams(60, 60));
		// v.setLayoutParams(new Gallery.LayoutParams(130,130));
		tvName.setText(tempList.get(arg2).getPicName());
	}
	@Override
	public void onNothingSelected(AdapterView<?> arg0) {
		tvName.setText("Nothing selected .");
	}
});
```