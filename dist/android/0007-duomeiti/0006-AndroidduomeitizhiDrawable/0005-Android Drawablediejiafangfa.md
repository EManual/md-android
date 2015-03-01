大家可能知道Bitmap的叠加处理在Android平台中可以通过Canvas一层一层的画就行了，而Drawable中如何处理呢? 除了使用BitmapDrawable的getBitmap方法将Drawable转换为Bitmap外，今天我们就给大家说下好用简单的LayerDrawable类，LayerDrawable顾名思义就是层图形对象。下面直接用一个简单的代码表示:
```  
Bitmap bm = BitmapFactory.decodeResource(getResources(), R.drawable.cwj);
Drawable[] array = new Drawable[3];
array[0] = new PaintDrawable(Color.BLACK); // 黑色
array[1] = new PaintDrawable(Color.WHITE); // 白色
array[2] = new BitmapDrawable(bm); // 位图资源
LayerDrawable ld = new LayerDrawable(array); // 参数为上面的Drawable数组
ld.setLayerInset(1, 1, 1, 1, 1); // 第一个参数1代表数组的第二个元素，为白色
ld.setLayerInset(2, 2, 2, 2, 2); // 第一个参数2代表数组的第三个元素，为位图资源
mImageView.setImageDrawable(ld);
```
上面的方法中LayerDrawable是关键，EOE提示setLayerInset方法原型为public void setLayerInset (int index, int l, int t, int r, int b)其中第一个参数为层的索引号，后面的四个参数分别为left、top、right和bottom。对于简单的图片合成我们可以将第一和第二层的PaintDrawable换成BitmapDrawable即可实现简单的图片合成。