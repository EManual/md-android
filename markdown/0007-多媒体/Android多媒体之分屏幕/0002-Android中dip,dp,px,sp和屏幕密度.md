1. dip: device independent pixels(设备独立像素). 不同设备有不同的显示效果,这个和设备硬件有关，一般我们为了支持WVGA、HVGA和QVGA 推荐使用此单位   这个，不依赖像素。
这里要特别注意dip与屏幕密度有关，而屏幕密度又与具体的硬件有关，硬件设置不正确，有可能导致dip不能正常显示。在屏幕密度为160的显示屏上，1dip=1px，有时候可能你的屏幕分辨率很大如480*800，但是屏幕密度没有正确设置比如说还是160，那么这个时候凡是使用dip的都会显示异常，基本都是显示过小。
dip的换算：
```  
dip（value）=(int) (px（value）/1.5 + 0.5)
```
2. dp: 很简单，和dip是一样的。
3. px: pixels(像素)，不同的设备不同的显示屏显示效果是相同的，这是绝对像素，是多少就永远是多少不会改变。
4.  sp: scaled pixels(放大像素). 主要用于字体显示best for textsize。
备注: 根据google的推荐，像素统一使用dip，字体统一使用sp  
举个例子区别px和dip：
px就是像素，如果用px,就会用实际像素画，比个如吧，用画一条长度为240px的横线，在480宽的模拟器上看就是一半的屏宽，而在320宽的模拟器上看就是2／3的屏宽了。
而dip，就是把屏幕的高分成480分，宽分成320分。比如你做一条160dip的横线，无论你在320还480的模拟器上，都是一半屏的长度。
```  
public static int dip2px(Context context, float dipValue){
	final float scale = context.getResources().getDisplayMetrics().density;
	return (int)(dipValue * scale + 0.5f);
}
public static int px2dip(Context context, float pxValue){
	final float scale = context.getResources().getDisplayMetrics().density;
	return (int)(pxValue / scale + 0.5f);
}
```