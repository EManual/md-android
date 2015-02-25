2. 获取位图的信息
要获取位图信息，比如位图大小、像素、density、透明度、颜色格式等，获取得到Bitmap就迎刃而解了，这些信息在Bitmap的手册中，这里只是辅助说明以下2点：
*在Bitmap中对RGB颜色格式使用Bitmap.Config定义，仅包括ALPHA_8、ARGB_4444、ARGB_8888、RGB_565，缺少了一些其他的，比如说RGB_555，在开发中可能需要注意这个小问题；
*Bitmap还提供了compress()接口来压缩图片，不过AndroidSAK只支持PNG、JPG格式的压缩；其他格式的需要Android开发人员自己补充了。
3. 显示位图
显示位图可以使用核心类Canvas，通过Canvas类的drawBirmap()显示位图，或者借助于BitmapDrawable来将Bitmap绘制到Canvas。当然，也可以通过BitmapDrawable将位图显示到View中。
转换为BitmapDrawable对象显示位图
```  
// 获取位图
Bitmap bmp=BitmapFactory.decodeResource(res, R.drawable.pic180);
// 转换为BitmapDrawable对象
BitmapDrawable bmpDraw=new BitmapDrawable(bmp);
// 显示位图
ImageView iv2 = (ImageView)findViewById(R.id.ImageView02);
iv2.setImageDrawable(bmpDraw);
```
使用Canvas类显示位图
这儿采用一个继承自View的子类Panel，在子类的OnDraw中显示
```  
public class MainActivity extends Activity {
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
			Bitmap bmp = BitmapFactory.decodeResource(getResources(), R.drawable.pic180);
			canvas.drawColor(Color.BLACK);
			canvas.drawBitmap(bmp, 10, 10, null);
		}
	}
}
```
4. 位图缩放
（1）将一个位图按照需求重画一遍，画后的位图就是我们需要的了，与位图的显示几乎一样：drawBitmap(Bitmap bitmap, Rect src, Rect dst, Paint paint)。
（2）在原有位图的基础上，缩放原位图，创建一个新的位图：CreateBitmap(Bitmap source, int x, int y, int width, int height, Matrix m, boolean filter)
（3）借助Canvas的scale(float sx, float sy) （Preconcat the current matrix with the specified scale.），不过要注意此时整个画布都缩放了。
（4）借助Matrix：
```  
Bitmap bmp = BitmapFactory.decodeResource(getResources(), R.drawable.pic180); 
Matrix matrix=new Matrix();
matrix.postScale(0.2f, 0.2f);
Bitmap dstbmp=Bitmap.createBitmap(bmp,0,0,bmp.getWidth(),
bmp.getHeight(),matrix,true);
canvas.drawColor(Color.BLACK); 
canvas.drawBitmap(dstbmp, 10, 10, null);
```
5. 位图旋转
同样，位图的旋转也可以借助Matrix或者Canvas来实现。
```  
Bitmap bmp = BitmapFactory.decodeResource(getResources(), R.drawable.pic180); 
Matrix matrix=new Matrix();
matrix.postScale(0.8f, 0.8f);//缩放
matrix.postRotate(45);//旋转
Bitmap dstbmp=Bitmap.createBitmap(bmp,0,0,bmp.getWidth(),
bmp.getHeight(),matrix,true);
canvas.drawColor(Color.BLACK);
canvas.drawBitmap(dstbmp, 10, 10, null);
```