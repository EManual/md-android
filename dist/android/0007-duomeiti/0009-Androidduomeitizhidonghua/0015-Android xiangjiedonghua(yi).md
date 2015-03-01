#### Android 动画使用示例
使用动画示例程序的效果是点击按钮，TextView 旋转一周。读者也可以参看 Apidemos 中包 com.example.android.apis.animationview 下面的 Transition3d 和 com.example.android.apis.view 下面的 Animation1/Animation2/Animation3 示例代码。
清单 1. 代码直接使用动画
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.animation.AccelerateDecelerateInterpolator;
import android.view.animation.Animation;
import android.view.animation.RotateAnimation;
import android.widget.Button;
public class TestAnimation extends Activity implements OnClickListener {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button btn = (Button) findViewById(R.id.Button);
		btn.setOnClickListener(this);
	}
	public void onClick(View v) {
		Animation anim = null;
		anim = new RotateAnimation(0.0f, +360.0f);
		anim.setInterpolator(new AccelerateDecelerateInterpolator());
		anim.setDuration(3000);
		findViewById(R.id.TextView01).startAnimation(anim);
	}
}
```
其中的 java 代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.Button;
import android.widget.TextView;
public class TestAnimation extends Activity implements OnClickListener {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button btn = (Button) findViewById(R.id.Button01);
		btn.setOnClickListener(this);
	}
	@Override
	public void onClick(View v) {
		Animation anim = AnimationUtils.loadAnimation(this,
				R.anim.my_rotate_action);
		findViewById(R.id.TextView01).startAnimation(anim);
	}
}
```