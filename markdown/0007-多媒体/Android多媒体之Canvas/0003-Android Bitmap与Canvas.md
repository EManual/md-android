1. 从资源中获取位图
可以使用BitmapDrawable或者BitmapFactory来获取资源中的位图。
```  
Resources res=getResources();
Resources res=getResources();
```
使用BitmapDrawable (InputStream is)构造一个BitmapDrawable;
```  
InputStream is = res.openRawResource(R.drawable.pic180);
BitmapDrawable bmpDraw = new BitmapDrawable(is);
Bitmap bmp = bmpDraw.getBitmap();
```
使用BitmapDrawable类的getBitmap()获取得到位图;
```  
InputStream is = res.openRawResource(R.drawable.pic180);
BitmapDrawable bmpDraw = new BitmapDrawable(is);
Bitmap bmp = bmpDraw.getBitmap();
```
2. 获取位图的信息
要获取位图信息，比如位图大小、像素、density、透明度、颜色格式等。
3. 显示位图
```  
// 获取位图
Bitmap bmp = BitmapFactory.decodeResource(res, R.drawable.pic180);
// 转换为BitmapDrawable对象
BitmapDrawable bmpDraw = new BitmapDrawable(bmp);
// 显示位图
ImageView iv2 = (ImageView) findViewById(R.id.ImageView02);
iv2.setImageDrawable(bmpDraw);
// 获取位图
Bitmap bmp = BitmapFactory.decodeResource(res, R.drawable.pic180);
// 转换为BitmapDrawable对象
BitmapDrawable bmpDraw = new BitmapDrawable(bmp);
// 显示位图
ImageView iv2 = (ImageView) findViewById(R.id.ImageView02);
iv2.setImageDrawable(bmpDraw);
// 使用Canvas类显示位图
// 这儿采用一个继承自View的子类Panel，在子类的OnDraw中显示
@Override
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(new Panel(this));
}

class Panel extends View {
	public Panel(Context context) {
		super(context);
	}

	public void onDraw(Canvas canvas) {
		Bitmap bmp = BitmapFactory.decodeResource(getResources(),
				R.drawable.pic180);
		canvas.drawColor(Color.BLACK);
		canvas.drawBitmap(bmp, 10, 10, null);
	}
}
```