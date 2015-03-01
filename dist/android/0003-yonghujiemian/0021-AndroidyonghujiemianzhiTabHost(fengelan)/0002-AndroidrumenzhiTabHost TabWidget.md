这回要介绍的是Android的Tab控件，Tab控件可以达到分页的效果，让一个屏幕的内容尽量丰富，当然也会增加开发的复杂程度，在有必要的时候再使用。Android的Tab控件使用起来有点奇怪，必须包含和按照以下的顺序：
![img](P)  
TabHost控件->TabWidget(必须命名为tabs)->FrameLayout(必须命名为tabcontent)。
接下来贴出本例运行的截图：
![img](P)  
main.xml的源码： 
```  
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/TabHost1"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <TabWidget
        android:id="@android:id/tabs"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
    </TabWidget>
    <FrameLayout
        android:id="@android:id/tabcontent"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:paddingTop="65px" >
        <LinearLayout
            android:id="@+id/Tab1"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical" >
            <EditText
                android:id="@+id/edtTab1"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content" >
            </EditText>
            <Button
                android:id="@+id/btnTab1"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Tab1" >
            </Button>
        </LinearLayout>
        <LinearLayout
            android:id="@+id/Tab2"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal" >
            <EditText
                android:id="@+id/edtTab2"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="300" >
            </EditText>
            <Button
                android:id="@+id/btnTab2"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Tab2" >
            </Button>
        </LinearLayout>
    </FrameLayout>
</TabHost>
```
程序源码:
```   
public class testTab extends TabActivity {// 基于TabActivity构建
	Button btnTab1, btnTab2;
	EditText edtTab1, edtTab2;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		TabHost tabs = getTabHost();
		// 设置Tab1
		TabSpec tab1 = tabs.newTabSpec("tab1");
		tab1.setIndicator("tab1"); // 设置tab1的名称
		tab1.setContent(R.id.Tab1); // 关联控件
		tabs.addTab(tab1); // 添加tab1
		btnTab1 = (Button) this.findViewById(R.id.btnTab1);
		edtTab1 = (EditText) this.findViewById(R.id.edtTab1);
		btnTab1.setOnClickListener(new ClickEvent());
		// 设置Tab2
		TabSpec tab2 = tabs.newTabSpec("tab2");
		tab2.setIndicator("tab2");
		tab2.setContent(R.id.Tab2);
		tabs.addTab(tab2);
		btnTab2 = (Button) this.findViewById(R.id.btnTab2);
		edtTab2 = (EditText) this.findViewById(R.id.edtTab2);
		btnTab2.setOnClickListener(new ClickEvent());
		tabs.setCurrentTab(0);
	}
	class ClickEvent implements View.OnClickListener {
		@Override
		public void onClick(View v) {
			if (v == btnTab1) {
				edtTab1.setText("tab1");
			} else if (v == btnTab2) {
				edtTab2.setText("tab2");
			}
		}
	}
}
```