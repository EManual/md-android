下面图片中的确定按钮的效果。
![img](P)  
也就是弹出菜单。
主activity布局 

```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/com_example_menueffect_effect1_relativeLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
    <ListView
        android:id="@+id/com_example_menueffect_effect1_listView1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignTop="@id/com_example_menueffect_effect1_relativeLayout" >
    </ListView>
    <Button
        android:id="@+id/com_example_menueffect_effect1_btnOk"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true
        android:layout_alignParentLeft="true
        android:text="确定" >
    </Button>
    <Button
        android:id="@+id/com_example_menueffect_effect1_btnCancel"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true
        android:layout_alignParentRight="true
        android:text="取消" >
    </Button>
</RelativeLayout>
```

popupmenu布局 

```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <Button
        android:id="@+id/com_example_menueffect_effect1_popupwindow_btnOpen"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="打开" >
    </Button>
    <Button
        android:id="@+id/com_example_menueffect_effect1_popupwindow_btnSave"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="保存" >
    </Button>
    <Button
        android:id="@+id/com_example_menueffect_effect1_popupwindow_btnExit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="退出" >
    </Button>
</LinearLayout>
```

主activity代码 

```  
import android.app.Activity;
import android.os.Bundle;
import android.view.Gravity;
import android.view.LayoutInflater;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.ListView;
import android.widget.PopupWindow;
public class Effect1Activity extends Activity {
	Button m_btnOk;
	Button m_btnCancel;
	ListView m_lv;
	String[] m_users;
	PopupWindow m_popupWindow;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(com.example.R.layout.com_example_menueffect_effect1);
		m_users = new String[] { "ssssss", "bbbbbbb", "sfdsdfsdfsfd" };
		m_btnOk = (Button) findViewById(com.example.R.id.com_example_menueffect_effect1_btnOk);
		m_btnCancel = (Button) findViewById(com.example.R.id.com_example_menueffect_effect1_btnCancel);
		m_lv = (ListView) findViewById(com.example.R.id.com_example_menueffect_effect1_listView1);
		ArrayAdapter<String> arrayAdapter = new ArrayAdapter<String>(this,
				android.R.layout.simple_list_item_1, m_users);
		m_lv.setAdapter(arrayAdapter);
		m_btnOk.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				LayoutInflater layoutInflater = getLayoutInflater();
				View view = layoutInflater
						.inflate(
								com.example.R.layout.com_example_menueffect_effect1_popupwindow,
								null);
				m_popupWindow = new PopupWindow(view, 100, 200);
				m_popupWindow.showAsDropDown(m_btnOk, 0, m_btnOk.getHeight());
			}
		});
	}
}
```