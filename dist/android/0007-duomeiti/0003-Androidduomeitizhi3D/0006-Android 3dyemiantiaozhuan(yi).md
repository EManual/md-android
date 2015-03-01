我们今天来说说怎么样用3D页面跳转。
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
public class Layout3D extends Activity {
	private int mCenterX = 160;
	private int mCenterY = 0;
	private ViewGroup layout1;
	private ViewGroup layout2;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		layout1 = (ViewGroup) findViewById(R.id.layout1);
		Button b1 = (Button) findViewById(R.id.button1);
		b1.setEnabled(true);
		b1.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				leftMoveHandle();
				v.setEnabled(false);
			}
		});
	}
	public void jumpToLayout1(Rotate3d leftAnimation) {
		setContentView(R.layout.main);
		layout1 = (ViewGroup) findViewById(R.id.layout1);
		layout1.startAnimation(leftAnimation);
		Button b1 = (Button) findViewById(R.id.button1);
		b1.setEnabled(true);
		b1.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				leftMoveHandle();
			}
		});
	}
	public void jumpToLayout2(Rotate3d rightAnimation) {
		setContentView(R.layout.mylayout);
		layout2 = (ViewGroup) findViewById(R.id.layout2);
		layout2.startAnimation(rightAnimation);
		Button b2 = (Button) findViewById(R.id.button2);
		b2.setEnabled(true);
		b2.setOnClickListener(new Button.OnClickListener() {
			public void onClick(View v) {
				rightMoveHandle();
			}
		});
	}
	public void leftMoveHandle() {
		Rotate3d leftAnimation = new Rotate3d(0, -90, 0, 0, mCenterX, mCenterY);
		Rotate3d rightAnimation = new Rotate3d(90, 0, 0.0f, 0.0f, mCenterX,
				mCenterY);
		leftAnimation.setFillAfter(true);
		leftAnimation.setDuration(1000);
		rightAnimation.setFillAfter(true);
		rightAnimation.setDuration(1000);
		layout1.startAnimation(leftAnimation);
		jumpToLayout2(rightAnimation);
	}
	public void rightMoveHandle() {
		Rotate3d leftAnimation = new Rotate3d(0, 90, 0, 0, mCenterX, mCenterY);
		Rotate3d rightAnimation = new Rotate3d(-90, 0, 0.0f, 0.0f, mCenterX,
				mCenterY);
		leftAnimation.setFillAfter(true);
		leftAnimation.setDuration(1000);
		rightAnimation.setFillAfter(true);
		rightAnimation.setDuration(1000);
		layout2.startAnimation(rightAnimation);
		jumpToLayout1(leftAnimation);
	}
}
```