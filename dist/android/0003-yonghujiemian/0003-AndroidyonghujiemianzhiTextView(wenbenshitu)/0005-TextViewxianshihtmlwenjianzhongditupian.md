我们知道要让TextView解析和显示Html代码。可以使用Spanned text = Html.fromHtml(source);
tv.setText(text);来实现，这个用起来简单方便。但是，怎样让TextView也显示Html中<image>节点的图像呢？
fromHtml的重构：
```  
fromHtml(String source, Html.ImageGetter imageGetter, Html.TagHandler tagHandler)
```
实现一下ImageGetter就可以让图片显示了：
```  
ImageGetter imgGetter = new Html.ImageGetter() {
	@Override
	public Drawable getDrawable(String source) {
		Drawable drawable = null;
		drawable = Drawable.createFromPath(source);
		// Or fetch it from the
		// URL
		// Important
		drawable.setBounds(0, 0, drawable.getIntrinsicWidth(),
				drawable.getIntrinsicHeight());
		return drawable;
	}
};
```