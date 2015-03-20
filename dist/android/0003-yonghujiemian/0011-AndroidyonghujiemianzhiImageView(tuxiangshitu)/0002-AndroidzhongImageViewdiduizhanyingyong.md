在设计此范例之前，必须先准备三张图片（两张外框图、一张内框图），将这三张图片放在res/drawable下面，在此使用的图片为PNG图形文件，而图案大小最好是已经调整成符合手机屏幕大小，或者依据手机的分辨率，动态调整ImageView的大小。稍后的范例将介绍如何调整ImageView的大小，这里就不赘述了。
准备好之后，开始做这个酷炫的专业相框应用程序，在Layout当中创建了两个ImageView，且以绝对坐标的方式"堆栈"在一起，在其下方放上两个按钮（Button），按钮的目的是为了要用来切换图片，创建完成后，要在Button事件里处理置换图片的动作。
程序目的为单击Button1、ImageView1会出现right的图片，单击Button2、ImageView1会出现left的图片，而ImageView2皆为固定不动（文件名叫oa），这个范例并不难，先来看看范例运行结果。
效果图：
00
下面我们就来看看代码。
```  
/* import程序略 */
public class EX04_07 extends Activity {
	/* 声明 Button、ImageView对象 */
	private ImageView mImageView01;
	private ImageView mImageView02;
	private Button mButton01;
	private Button mButton02;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		/* 取得 Button、ImageView对象 */
		mImageView01 = (ImageView) findViewById(R.id.myImageView1);
		mImageView02 = (ImageView) findViewById(R.id.myImageView2);
		mButton01 = (Button) findViewById(R.id.myButton1);
		mButton02 = (Button) findViewById(R.id.myButton2);
		/* 设置ImageView背景图 */
		mImageView01.setImageDrawable(getResources().getDrawable(R.drawable.right));
		mImageView02.setImageDrawable(getResources().getDrawable(R.drawable.oa));
		/* 用OnClickListener事件来启动 */
		mButton01.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				/* 当启动后，ImageView立刻换背景图 */
				mImageView01.setImageDrawable(getResources().getDrawable(
						R.drawable.right));
			}
		});
		mButton02.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				mImageView01.setImageDrawable(getResources().getDrawable(R.drawable.left));
			}
		});
	}
}
```
建两个ImageView，一个为外框、另一个为内框，需注意的是，图片需要做一个排序堆栈顺序，将前景图放在上方（以AbsoluteLayout），将背景图放在前景图的下方，这是最简单的堆栈顺序方法。
```  
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget34"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <!-- 创建第一个ImageView (第二层图片) -->
    <ImageView
        android:id="@+id/myImageView1"
        android:layout_width="320px"
        android:layout_height="280px"
        android:layout_x="0px"
        android:layout_y="36px" />
    <!-- 创建第二个ImageView (第一层图片) -->
    <ImageView
        android:id="@+id/myImageView2"
        android:layout_width="104px"
        android:layout_height="157px"
        android:layout_x="101px"
        android:layout_y="119px" />
    <!-- 创建第一个Button -->
    <Button
        android:id="@+id/myButton1"
        android:layout_width="105px"
        android:layout_height="66px"
        android:layout_x="9px"
        android:layout_y="356px"
        android:text="pic1" />
    <!-- 创建第二个Button -->
    <Button
        android:id="@+id/myButton2"
        android:layout_width="105px"
        android:layout_height="66px"
        android:layout_x="179px"
        android:layout_y="356px"
        android:text="pic2" />
</AbsoluteLayout>
```