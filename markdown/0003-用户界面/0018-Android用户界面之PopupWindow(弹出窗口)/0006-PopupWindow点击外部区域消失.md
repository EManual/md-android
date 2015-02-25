点击PopupWindow 外部区域消失
```  
// view 是自定义的点击PopupWindow 样式
pop = new PopupWindow(view, ViewGroup.LayoutParams.FILL_PARENT,
		ViewGroup.LayoutParams.WRAP_CONTENT);
pop.setBackgroundDrawable(new BitmapDrawable());
pop.setOutsideTouchable(true);
pop = new PopupWindow(view, ViewGroup.LayoutParams.FILL_PARENT,
		ViewGroup.LayoutParams.WRAP_CONTENT);
pop.setBackgroundDrawable(new BitmapDrawable());
pop.setOutsideTouchable(true);
```
上面两句位置不能颠倒，不然无效！（经本机测试 不知道别人如何）必须设置backgroundDrawable()。