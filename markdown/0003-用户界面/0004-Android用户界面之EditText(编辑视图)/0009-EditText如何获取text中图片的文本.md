我现在有这样的一个需求，输入框做了，支持表情的功能，请看图

![img](http://emanual.github.io/md-android/img/view_edittext/10_edittext.jpg) 

支持了图片是支持了，但是我现在要获取到EditText的文本的时候，去发现，其中图片的部分就变成了下图obj的那个样子，这样的话，我要将他解析成服务器能认识的形式，那根本就是不可能的呀！！
请问大家有什么办法么？？我让EditText 显示图片的方法用的是 
```  
ImageGetter imageGetter = new ImageGetter() {
	@Override
	public Drawable getDrawable(String source) {
		int id = Integer.parseInt(source);
		Drawable d = getResources().getDrawable(id);
		d.setBounds(0, 0, d.getIntrinsicWidth(), d.getIntrinsicHeight());
		return d;
	}
};
// // 将图片转为了 字符序列
CharSequence cs1 = Html.fromHtml("<img src='" + id + "'/>", imageGetter,
		null);
```
但是这样是达到了显示的效果，不过要是真正获取EditText里面的文本，就会出现第二副图，中的文本样子，这样的话我就不能正确解析和操作了
解决方法如下，其实我们如果获取所见即所得的结果的文本的话，那么我们再编辑文本的时候可以利用Spannable类的文本处理
下面只要把我插入图片的也就是 贴出来的代码 换成 下面这样就可以了
```  
// 第一个参数 是图片要的名称，可以自己取 第二个参数就是 图片资源ID
private void setFace(String faceTitle, int faceImg) {
	int start = edit.getSelectionStart();
	Spannable ss = edit.getText().insert(start, "[" + faceTitle + "]");
	Drawable d = getResources().getDrawable(faceImg);
	d.setBounds(0, 0, d.getIntrinsicWidth(), d.getIntrinsicHeight());
	ImageSpan span = new ImageSpan(d, faceTitle + ".gif",
			ImageSpan.ALIGN_BASELINE);
	ss.setSpan(span, start, start + ("[" + faceTitle + "]").length(),
			Spannable.SPAN_EXCLUSIVE_EXCLUSIVE);
}
```

![img](http://emanual.github.io/md-android/img/view_edittext/10_edittext2.jpg)