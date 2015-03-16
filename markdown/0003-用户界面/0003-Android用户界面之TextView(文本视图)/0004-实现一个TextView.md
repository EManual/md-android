自定义View
[功能]
1. 自定义View 实现 TextView 的功能：
2. 典型的 TextView 如下：
```  
<TextView 
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:text="HelloTextView" />
```
写道：
```  
android:text="HelloTextView"
```
就会显示指定的String
[代码]
1.  定义Text2View 所用关键字"text" 用于指定显示用的string 
* 在 res/value 目录下创建 attrs.xml 文件 如下定义：
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
	<declare-styleable name="Text2View">
		<attr name="text" format="string" />
	</declare-styleable>
</resources> 
```
2. 定义 public class Text2View extends View
```  
public class Text2View extends View {
	Paint text2Paint;
	String text2Text;
	int ascent;
	// Constructor - for java
	public Text2View(Context context) {
		super(context);
		initialize();
	}
	// Constructor - for xml
	public Text2View(Context context, AttributeSet attrs) {
		super(context, attrs);
		initialize();
		TypedArray ta = context.obtainStyledAttributes(attrs,
				R.styleable.Text2View);
		int n = ta.getIndexCount();
		for (int i = 0; i < n; i++) {
			int attr = ta.getIndex(i);
			switch (attr) {
			case R.styleable.Text2View_text:
				updateText(ta.getString(R.styleable.Text2View_text));
				break;
			// TO ADD CUSTOM ATTRIBUTE
			default:
				break;
			}
		}
		ta.recycle();
	}
	private final void updateText(String s) {
		text2Text = s;
		requestLayout();
		invalidate();
	}
	// load default setting on Text2View
	private final void initialize() {
		text2Paint = new Paint();
		text2Paint.setAntiAlias(true);
		text2Paint.setTextSize(16);
		text2Paint.setColor(0xFF000000);
		// setPadding(3, 3, 3, 3);
	}
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		setMeasuredDimension(measureWidth(widthMeasureSpec),
				measureHeight(heightMeasureSpec));
	}
	private int measureWidth(int measureSpec) {
		int result = 0;
		int specMode = MeasureSpec.getMode(measureSpec);
		int specSize = MeasureSpec.getSize(measureSpec);
		if (specMode == MeasureSpec.EXACTLY) {
			// We were told how big to be
			result = specSize;
		} else {
			// Measure the text
			result = (int) text2Paint.measureText(text2Text) + getPaddingLeft()
					+ getPaddingRight();
			if (specMode == MeasureSpec.AT_MOST) {
				// Respect AT_MOST value if that was what is called for by
				// measureSpec
				result = Math.min(result, specSize);
			}
		}
		return result;
	}
	 /**
	  * Determines the height of this view
	  * @param measureSpec
	  *            A measureSpec packed into an int
	  * @return The height of the view, honoring constraints from measureSpec
	  */
	private int measureHeight(int measureSpec) {
		int result = 0;
		int specMode = MeasureSpec.getMode(measureSpec);
		int specSize = MeasureSpec.getSize(measureSpec);
		ascent = (int) text2Paint.ascent();
		if (specMode == MeasureSpec.EXACTLY) {
			// We were told how big to be
			result = specSize;
		} else {
			// Measure the text (beware: ascent is a negative number)
			result = (int) (-ascent + text2Paint.descent()) + getPaddingTop()
					+ getPaddingBottom();
			if (specMode == MeasureSpec.AT_MOST) {
				// Respect AT_MOST value if that was what is called for by
				// measureSpec
				result = Math.min(result, specSize);
			}
		}
		return result;
	}
	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		canvas.drawText(text2Text, getPaddingLeft(), getPaddingTop() - ascent,
				text2Paint);
	}
}
```
3. Text2View 构造函数需要解释一下：
```  
// Constructor - for java
public Text2View(Context context) {
	super(context);
	initialize();
}
// Constructor - for xml
public Text2View(Context context, AttributeSet attrs) {
	super(context, attrs);
	initialize();
	TypedArray ta = context.obtainStyledAttributes(attrs,
			R.styleable.Text2View);
	int n = ta.getIndexCount();
	for (int i = 0; i < n; i++) {
		int attr = ta.getIndex(i);

		switch (attr) {
		case R.styleable.Text2View_text:
			updateText(ta.getString(R.styleable.Text2View_text));
			break;
		// TO ADD CUSTOM ATTRIBUTE
		default:
			break;
		}
	}
	ta.recycle();
}
```
4. 在 xml 文件中如何使用Text2View
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="HelloTextView" />
</LinearLayout>
```
5. 效果图 和默认的TextView 比较一下

![img](http://emanual.github.io/md-android/img/view_textview/05_textview.jpg) 