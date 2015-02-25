当我们的软件基本功能都实现了之后，我们是不是还可以把它做的更好呢？一个能让用户体验明显提升的方法，就是为我们的应用适当的增加一些动画效果，Android系统中已经为我们内置了几个常用的动画，基本满足我们日常的需要。但是如果我们的需求比较特殊，需要实现自己特定的动画效果又改怎么办呢？下面就为大家介绍一下。
整体来说Android中的动画，就是一个线性变换，学过线性代数的同学肯定不陌生拉～通过增加变换矩阵，就可以实现我们向要的各种效果，当然，如果你的数学不是很好，也没有关系，因为具体的数学细节android已经封装很多了，我们要做的只是调用相应的接口就可以了。
首先，我们要继承Animation类，并实现它的applyTransformation方法，这个方法是一个回调方法，在动画进行的过程中，系统会一直不停的调用这个方法，每次调用，这个方法给我们传进来一个变换矩阵，通过对这个矩阵的操作，我们就可以实现自己的动画效果了，例如下面的代码：
```  
public class MyAnimation extends Animation {
	private int halfWidth;
	private int halfHeight;
	@Override
	public void initialize(int width, int height, int parentWidth,
			int parentHeight) {
		super.initialize(width, height, parentWidth, parentHeight);
		setDuration(1500);
		setFillAfter(true);
		halfWidth = width / 2;
		halfHeight = height / 2;
		setInterpolator(new LinearInterpolator());
	}
	@Override
	protected void applyTransformation(float interpolatedTime, Transformation t) {
		final Matrix matrix = t.getMatrix();
		matrix.preScale(interpolatedTime, interpolatedTime);
		matrix.preRotate(interpolatedTime * 360);
		matrix.preTranslate(-halfWidth, -halfHeight);
		matrix.postTranslate(halfWidth, halfHeight);
	}
}
```
在initialize方法中，我们首先设置了动画的时间，1.5秒，然后是setFillAfter，这个方法设置成true的意思是在动画结束后保持动画效果，如果这里不明白可以忽略这个，不影响大局。接下来是保存动画对象的中点坐标，随后会用到，最后一行的意思是线性动画，这个的意思就是整个动画的速率是不变的，也就是线性的。
在下面的applyTransformation方法中，我们首先取得到了变换矩阵，然后对这个矩阵进行了两个变换操作：
```   
matrix.preScale(interpolatedTime, interpolatedTime);       
```  
这个方法是进行缩放，传给它的参数interpolatedTime代表当前方法掉用时，动画进行的一个时间点，这个值的范围是0到1，也就是说动画刚开始的时候传进来的interpolatedTime为0，动画进行中的时候，传进来的是0到1之间的小数，动画结束的时候传进来的是1。
而preScale方法接受的两个参数也是0到1，代表缩放的比例，0是最小，1是原始尺寸。
所以这个变换的意思就是，以当前动画进行的时间为参考，逐渐放大我们的可见对象。再说白了，就是从一个点，慢慢放大，最后恢复到原始尺寸，这样的效果。
```  
matrix.preRotate(interpolatedTime * 360);   
```  
这个方法是旋转动画，和上面的一样，根据动画的时间，将可见对象旋转一周，应该不难理解。
这两个变换叠加再一起，就实现了一个这样的效果，我们的可见对象从画面正中央，旋转着逐渐放大，最终充满可见区域。  
```  
matrix.preTranslate(-halfWidth, -halfHeight);
matrix.postTranslate(halfWidth, halfHeight);   
```  
这两行代码意思可能就不那么明显了，先说如果不加这两行代码，会是一个什么情况，默认情况下，动画是以对象的左上角为起点的，如果所以我们前面用到的halfWidth，halfHeight就用到了，这里保存了可见对象的一半宽度和高度，也就是中点，使用上面这两个方法后，就会改变动画的起始位置，动画默认是从右下角开始扩大的，使用matrix.preTranslate(-halfWidth,-halfHeight)就把扩散点移到了中间，同样，动画的起始点为左上角，使用matrix.postTranslate(halfWidth, halfHeight)就把起始点移到了中间，这样就实现我们期望的效果了。
接下来我们要做的就是应用这个动画，首先需要一个Activity，和一个布局文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <Button
        android:id="@+id/btn_animate"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="开始动画" />
    <ListView
        android:id="@+id/list_view_id"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:persistentDrawingCache="animation|scrolling" />
</LinearLayout>
```
这里定义了一个ListView，我们将用它作为我们的动画对象，下面是Activity的代码：
```  
public class Main extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        ListView list = (ListView)findViewById(R.id.list_view_id);
        String[] data = new String[] {"测试数据","测试数据","测试数据","测试数据","测试数据","测试数据","测试数据"};
		ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1,data);
        list.setAdapter(adapter);
        Button b = (Button)this.findViewById(R.id.btn_animate);
        b.setOnClickListener(new OnClickListener() {
            public void onClick(View v) {
                ListView list = (ListView)findViewById(R.id.list_view_id);
                list.startAnimation(new MyAnimation());
            }
        });
    }
}
```
这里通过一个按钮来开启动画效果，关键的代码就是这一行：
```  
list.startAnimation(new MyAnimation());
```