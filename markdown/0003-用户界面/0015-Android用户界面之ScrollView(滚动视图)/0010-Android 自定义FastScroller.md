我们先来看看效果图
![img](P)  
设置快速滚动属性很容易，只需在布局的xml文件里设置属性即可：
但是有时候会发现设置属性无效，滚动ListView并未出现滑块。原因是该属性生效有最小记录限制。当ListView记录能够在4屏以内显示（也就是说滚动4页）就不会出现滑块。可能是api设计者认为这么少的记录不需要快速滚动。
我的依据是android源代码，见FastScroller的常量声明：
```  
// 最小的数字显示页面来证明快速卷轴
private static int MIN_PAGES = 4;
// 有足够的页面需要非常快速滚动吗?只有当总数变化接器会重新计算
if (mItemCount != totalItemCount && visibleItemCount > 0) {
	mItemCount = totalItemCount;
	mLongList = mItemCount / visibleItemCount >= MIN_PAGES;
}
```
通篇查看了ListView及其超累AbsListView，都未找到自定义图片的设置接口。看来是没打算让开发者更改了。但是用户要求我们自定义这个图片。那只能用非常手段了。
经过分析发现，该图片是ListView超类AbsListView的一个成员mFastScroller对象的成员mThumbDrawable。这里mThumbDrawable是Drawable类型的。mFastScroller是FastScroller类型，这个类型比较麻烦，类的声明没有modifier，也就是default（package），只能供包内的类调用。
因此反射代码写的稍微麻烦一些：
```  
try {
	Field f = AbsListView.class.getDeclaredField("mFastScroller");
	f.setAccessible(true);
	Object o = f.get(listView);
	f = f.getType().getDeclaredField("mThumbDrawable");
	f.setAccessible(true);
	Drawable drawable = (Drawable) f.get(o);
	drawable = getResources().getDrawable(R.drawable.icon);
	f.set(o, drawable);
	Toast.makeText(this, f.getType().getName(), 1000).show();
} catch (Exception e) {
	throw new RuntimeException(e);
}
```
这样我们就做完了，我们就可以改变默认的滑块图片了。大家看了以后是不是很简单呀。