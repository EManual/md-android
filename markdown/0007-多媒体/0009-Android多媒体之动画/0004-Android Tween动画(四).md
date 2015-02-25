在程序中直接实现
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.animation.AlphaAnimation;
import android.view.animation.Animation;
import android.view.animation.RotateAnimation;
import android.view.animation.ScaleAnimation;
import android.view.animation.TranslateAnimation;
import android.widget.Button;
import android.widget.ImageView;
public class TweenActivity extends Activity {
	Button btn1, btn2, btn3, btn4;
	ImageView img;
	// 定义Alpha动画
	private Animation alpha = null;
	// 定义Scale动画
	private Animation scale = null;
	// 定义Translate动画
	private Animation translate = null;
	// 定义Rotate动画
	private Animation rotate = null;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 装载图片资源
		img = (ImageView) findViewById(R.id.imgview);
		btn1 = (Button) findViewById(R.id.btn1);
		btn2 = (Button) findViewById(R.id.btn2);
		btn3 = (Button) findViewById(R.id.btn3);
		btn4 = (Button) findViewById(R.id.btn4);
		// 定义Alpha动画
		btn1.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				// 创建Alpha动画
				alpha = new AlphaAnimation(0.1f, 1.0f);
				// 设置动画时间为5秒
				alpha.setDuration(5000);
				// 开始播放
				img.startAnimation(alpha);
			}
		});
		// 定义Scale动画
		btn2.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				scale = new ScaleAnimation(0f, 1f, 0f, 1f,
						Animation.RELATIVE_TO_SELF, 0.5f,
						Animation.RELATIVE_TO_SELF, 0.5f);
				// 设置动画持续时间
				scale.setDuration(5000);
				// 开始动画
				img.startAnimation(scale);
			}
		});
		// 定义Translate动画
		btn3.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				translate = new TranslateAnimation(10, 100, 10, 100);
				// 设置动画持续时间
				translate.setDuration(3000);
				// 开始动画
				img.startAnimation(translate);
			}
		});
		// 定义Rotate动画
		btn4.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				rotate = new RotateAnimation(0f, +360f,
						Animation.RELATIVE_TO_SELF, 0.5f,
						Animation.RELATIVE_TO_SELF, 0.5f);
				rotate.setDuration(3000);
				img.startAnimation(rotate);
			}
		});
	}
}
```