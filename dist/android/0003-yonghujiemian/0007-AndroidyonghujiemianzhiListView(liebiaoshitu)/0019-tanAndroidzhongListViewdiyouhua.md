Adapter的作用是界面与数据之间的桥梁，通过设置适配器至ListView控件后(如调用ListView的 setAdapter(ListAdapter adapter)
)，列表的每一项会显示至页面中。其实，当列表里的每一项显示到页面时，都会调用Adapter的getView方法返回一个View，如：
```  
@Override
public View getView(int position, View convertView, ViewGroup parent) {
	return super.getView(position, convertView, parent);
}
```
我们看一看下面的这段代码：
```  
public View getView(int position, View convertView, ViewGroup parent) {
	View newView = mInflater.inflate(R.layout.list_item, null);
	((TextView) newView .findViewById(R.id.text)).setText(DATA[position]);
	((ImageView) newView .findViewById(R.id.icon)).setImageBitmap(
	(position & 1) == 1 ? mIcon1 : mIcon2);
	return newView ;
}
```
上面的代码块中，我通过LayoutInflater.inflate(,)将Layout文件--layout.list_item转换为View。
(注：Layout也是View的子类，但在android中如果想将xml中的Layout转换为View放入.java代码中操作，只能通过Inflater，而不能通过findViewById())
这时，如果我的ITEM项有数以千条这样多或更多呢，再以上面代码块的写法，后果自己想想吧。