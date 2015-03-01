处在多媒体时代，没有图片显示怎么可以？幸好android为我们提供了图片显示的控件imageVIew，下面的程序将通过这个控件实现触摸屏幕更换显示的图片。
程序开始运行
![img](P)  
单击屏幕之后，更换图片
![img](P)  
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.ImageView;
import android.widget.LinearLayout;
public class MixView extends Activity {
	// 定义一个访问图片的数组,这些图片保存在drawable—mdpi文件夹下，我这里用了五张燕姿的照片，姿迷无处不在~~~~~~~
	int[] images = new int[] { R.drawable.sunyz_1, R.drawable.sunyz_2,
			R.drawable.sunyz_3, R.drawable.sunyz_4, R.drawable.sunyz_5, };
	int currentImg = 0;
	@Override
	public void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 获取LinearLayout布局容器
		LinearLayout main = (LinearLayout) findViewById(R.id.root);
		// 程序创建ImageView组件
		final ImageView image = new ImageView(this);
		// 将ImageView组件添加到LinearLayout布局容器中
		main.addView(image);
		// 初始化时显示第一张图片
		image.setImageResource(images[0]);
		image.setOnClickListener(new OnClickListener()
		{
			@Override
			public void onClick(View v)
			{
				if (currentImg >= 4)
				{
					currentImg = -1;
				}
				// 改变ImageView里显示的图片
				image.setImageResource(images[++currentImg]);
			}
		});
	}
}
```