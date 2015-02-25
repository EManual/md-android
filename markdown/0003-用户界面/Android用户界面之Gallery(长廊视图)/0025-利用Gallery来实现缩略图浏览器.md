我们都知道有的时候在iPhone手机上或者Windows上面看到动态的图片，可以通过鼠标或者手指触摸来移动它，产生动态的图片滚动效果，还可以根据你的点击或者触摸触发其他事件响应。同样的，在Android中也提供这这种实现，这就是通过Gallery在UI上实现缩略图浏览器。
我们来看看Gallery是如何来实现的，先把控件绑定出来，从哪里绑绑定？控件当然藏在布局文件中，这个首先需要在UI布局中声明，这里就不再赘述，只需知道ID为gallery。 
```  
Gallery gallery = (Gallery) findViewById(R.id.gallery);
```
一般情况下，我们在Android中要用到类似这种图片容器的控件，都需要为它指定一个适配器，让它可以把内容按照我们定义的方式来显示，因此我们来给它加一个适配器，至于这个适配器如何实现，后面接着来操作，这里只需知道这个适配器的类叫ImageAdapter。 
```  
gallery.setAdapter(new ImageAdapter(this));
```
下面就是非常重要的了，适配器可以说是最重要的，我们还得准备好图片，在这里我们准备了五张图片，我们用其ID来做索引，以便在适配器中使用。
```  
private Integer[] mps = { R.drawable.icon1, R.drawable.icon2,
	R.drawable.icon3, R.drawable.icon4, R.drawable.icon5 };
```
这里将开始定义适配器了，通过继承BaseAdapter用以实现的适配器。 
```  
public class ImageAdapter extends BaseAdapter {
	private Context mContext;
	public ImageAdapter(Context context) {
		mContext = context;
	}
	public int getCount() {
		return mps.length;
	}
	public Object getItem(int position) {
		return position;
	}
	public long getItemId(int position) {
		return position;
	}
	public View getView(int position, View convertView, ViewGroup parent) {
		ImageView image = new ImageView(mContext);
		image.setImageResource(mps[position]);
		image.setAdjustViewBounds(true);
		image.setLayoutParams(new Gallery.LayoutParams(
		LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT));
		return image;
	}
}
```
至此，整个Gallery基本都是先完成了，我们还需要为它添加一个监听器，否则这个缩略图浏览器就仅仅只可以看不能用了。 
```  
gallery.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
	@Override
	public void onItemSelected(AdapterView<?> parent, View v,
			int position, long id) {
		// 显示提示框“随时随地，即兴时代，ATAAW.COM！”
	}
	@Override
	public void onNothingSelected(AdapterView<?> arg0) {
		// 这里不做响应
	}
});
```