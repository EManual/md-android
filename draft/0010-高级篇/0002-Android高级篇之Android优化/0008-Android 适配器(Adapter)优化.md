ListView的Adapter的作用如下图所示：
![img](P)  
Adapter的作用就是ListView界面与数据之间的桥梁，当列表里的每一项显示到页面时，都会调用Adapter的getView方法返回一个View。想过没有？在我们的列表有1000000项时会是什么样的？是不是会占用极大的系统资源？
先看看下面的代码：
```  
public View getView(int position, View convertView, ViewGroup parent) {
	View item = mInflater.inflate(R.layout.list_item_icon_text, null);
	((TextView) item.findViewById(R.id.text)).setText(DATA[position]);
	((ImageView) item.findViewById(R.id.icon))
			.setImageBitmap((position & 1) == 1 ? mIcon1 : mIcon2);
	return item;
}
```
我们再来看看下面的代码：
```  
public View getView(int position, View convertView, ViewGroup parent) {
	if (convertView == null) {
		convertView = mInflater.inflate(R.layout.item, null);
	}
	((TextView) convertView.findViewById(R.id.text))
			.setText(DATA[position]);
	((ImageView) convertView.findViewById(R.id.icon))
			.setImageBitmap((position & 1) == 1 ? mIcon1 : mIcon2);
	return convertView;
}
```
```  
public View getView(int position, View convertView, ViewGroup parent) {
	ViewHolder holder;
	if (convertView == null) {
		convertView = mInflater.inflate(R.layout.list_item_icon_text, null);
		holder = new ViewHolder();
		holder.text = (TextView) convertView.findViewById(R.id.text);
		holder.icon = (ImageView) convertView.findViewById(R.id.icon);
		convertView.setTag(holder);
	} else {
		holder = (ViewHolder) convertView.getTag();
	}
	holder.text.setText(DATA[position]);
	holder.icon.setImageBitmap((position & 1) == 1 ? mIcon1 : mIcon2);
	return convertView;
}
static class ViewHolder {
	TextView text;
	ImageView icon;
}
```