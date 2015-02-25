我们大家都看过动画效果的文章，但今天的这一片我们主要讲的就是Fragment动画效果。我们怎么样才能实现那，我们一共分为了四个步骤，这四个步骤就是主布局(main.xml)实现、FirstFragment布局(first_fragment.xml)实现、SecondFragment布局(second_fragment.xml)实现、主Activity实现。那么我们就根据这四个步骤，我们自己动手来操作一下。那么我们先来看看第一个步骤，实现main的代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center_vertical"
        android:orientation="horizontal"
        android:padding="4dip" >
        <Button
            android:id="@+id/firstButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hide" />
        <fragment
            android:id="@+id/firstFragment"
            android:name="com.flora.FragmentAnimationActivity$FirstFragment"
            android:layout_width="0px"
            android:layout_height="wrap_content"
            android:layout_weight="1" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center_vertical"
        android:orientation="horizontal"
        android:padding="4dip" >
        <Button
            android:id="@+id/secondButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hide" />
        <fragment
            android:id="@+id/secondFragment"
            android:name="com.flora.FragmentAnimationActivity$SecondFragment"
            android:layout_width="0px"
            android:layout_height="wrap_content"
            android:layout_weight="1" />
    </LinearLayout>
</LinearLayout>
```
FirstFragment布局(first_fragment.xml)实现：
```  

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="4dip" >
    <TextView
        android:id="@+id/msg"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="0"
        android:paddingBottom="4dip" />
</LinearLayout>
```
SecondFragment布局(second_fragment.xml)实现：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <TextView
        android:id="@+id/msg"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="0"
        android:paddingBottom="4dip" />
</LinearLayout>
```
主Activity实现：
```  
import android.app.Activity;
import android.app.Fragment;
import android.app.FragmentManager;
import android.app.FragmentTransaction;
import android.graphics.Color;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.TextView;
public class FragmentAnimationActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		FragmentManager fm = getFragmentManager();
		addListener(R.id.firstButton, fm.findFragmentById(R.id.firstFragment));
		addListener(R.id.secondButton, fm.findFragmentById(R.id.secondFragment));
	}
	private void addListener(int buttonID, final Fragment fragment) {
		final Button button = (Button) findViewById(buttonID);
		button.setOnClickListener(new OnClickListener() {
			public void onClick(View v) {
				FragmentTransaction ft = getFragmentManager()
						.beginTransaction();
				ft.setCustomAnimations(android.R.animator.fade_in,
						android.R.animator.fade_out);
				if (fragment.isHidden()) {
					ft.show(fragment);
					button.setText("隐藏");
				} else {
					ft.hide(fragment);
					button.setText("显示");
				}
				ft.commit();
			}
		});
	}
	public static class FirstFragment extends Fragment {
		@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container,
				Bundle savedInstanceState) {
			View v = inflater
					.inflate(R.layout.first_fragment, container, false);
			TextView tv = (TextView) v.findViewById(R.id.msg);
			tv.setText("This is first fragment.");
			tv.setBackgroundColor(Color.GREEN);
			return v;
		}
	}
	public static class SecondFragment extends Fragment {
		@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container,
				Bundle savedInstanceState) {
			View v = inflater.inflate(R.layout.second_fragment, container,
					false);
			TextView tv = (TextView) v.findViewById(R.id.msg);
			tv.setText("This is second fragment.");
			tv.setBackgroundColor(Color.RED);
			return v;
		}
	}
}
```
在这里我们还是主要用到了view.LayoutInflater、app.FragmentTransaction、app.FragmentManager，大家可要记住了，还有就是我们最后的代码中return一个你要返回的值，这个要记住，千万不要忘记了。当你忘记以后，就不会出现你想要的效果了。