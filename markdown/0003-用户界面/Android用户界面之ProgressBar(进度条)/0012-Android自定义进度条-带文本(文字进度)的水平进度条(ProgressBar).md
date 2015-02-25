默认的进度条（ProgressBar）是没有文本进度提示的，为了更直观显示进度，自定义了ProgressBar控件来显示的文本进度。
```  
[Tags]/**
 [Tags]* 带文本提示的进度条
 [Tags]*/
public class TextProgressBar extends ProgressBar {
	private String text;
	private Paint mPaint;
	public TextProgressBar(Context context) {
		super(context);
		initText();
	}
	public TextProgressBar(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		initText();
	}
	public TextProgressBar(Context context, AttributeSet attrs) {
		super(context, attrs);
		initText();
	}
	@Override
	public void setProgress(int progress) {
		setText(progress);
		super.setProgress(progress);

	}
	@Override
	protected synchronized void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		Rect rect = new Rect();
		this.mPaint.getTextBounds(this.text, 0, this.text.length(), rect);
		int x = (getWidth() / 2) - rect.centerX();
		int y = (getHeight() / 2) - rect.centerY();
		canvas.drawText(this.text, x, y, this.mPaint);
	}
	// 初始化，画笔
	private void initText() {
		this.mPaint = new Paint();
		this.mPaint.setAntiAlias(true);
		this.mPaint.setColor(Color.WHITE);
	}
	// 设置文字内容
	private void setText(int progress) {
		int i = (int) ((progress * 1.0f / this.getMax()) * 100);
		this.text = String.valueOf(i) + "%";
	}
}
```