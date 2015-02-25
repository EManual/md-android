在Android里要实现树形菜单，都是用ExpandableList(也有高手自己继承ListView或者LinearLayout来做)，但是ExpandableList一般只能实现2级树形菜单......本文也依然使用ExpandableList，但是要实现的是3级树形菜单。
当用BaseExpandableListAdapter来实现二级树形菜单时，父项(getGroupView())和子项(getChildView())都是使用TextView。当要实现三级树形菜单时，子项(getChildView())就必须使用ExpandableList了.......另外还要定义结构体来方便调用三级树形的数据，二级树形菜单可以用如下：
```  
static public class TreeNode{
	Object parent;
	List< Object> childs = new ArrayList< Object>();
}
```
static public class TreeNode{ Object parent; List< Object> childs=new ArrayList< Object>(); }　　
三级树形菜单可以用如下，子项是二级树形菜单的结构体：
```  
static public class SuperTreeNode {
	Object parent;
	//二级树形菜单的结构体
	List< TreeViewAdapter.TreeNode> childs = new ArrayList< TreeViewAdapter.TreeNode>();
}
```
实现三级树形菜单有两点要注意的：　　
1、第二级也是个树形菜单，因此必须在第二级项目展开/回收时设置足够的空间来完全显示二级树形菜单; 2、在实现三级树形菜单时，发现菜单的方法都是用不了(如OnChildClickListener、OnGroupClickListener等)，因此要获得选中的数据就必须在外部定义好回调函数，然后在第二级生成二级树形菜单时回调这个外部函数。
```  
<?xml version = "1.0" encoding = "utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <LinearLayout
		android:id="@+id/LinearLayout01"
		android:layout_width="wrap_content"
		android:layout_height="wrap_content" >
		<Button
			android:id="@+id/btnNormal"
			android:layout_width="160dip"
			android:layout_height="wrap_content"
			android:text="两层结构" >
		</Button>
		<Button
			android:id="@+id/btnSuper"
			android:layout_width="160dip"
			android:layout_height="wrap_content"
			android:text="三层结构" >
		</Button>
	</LinearLayout>
    <ExpandableListView
		android:id="@+id/ExpandableListView01"
		android:layout_width="fill_parent"
		android:layout_height="fill_parent" >
	</ExpandableListView>
</LinearLayout>
```
testExpandableList.java是主类，调用其他工具类，源码如下：
```  
import java.util.List;
import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.ExpandableListView;
import android.widget.ExpandableListView.OnChildClickListener;
import android.widget.Toast;
public class testExpandableList extends Activity {
	[Tags]/** Called when the activity is first created. */
	ExpandableListView expandableList;
	TreeViewAdapter adapter;
	SuperTreeViewAdapter superAdapter;
	Button btnNormal, btnSuper;
	// Sample data set. children[i] contains the children (String[]) for
	// groups[i].
	public String[] groups = { "xxxx好友", "xxxx同学", "xxxxx女人" };
	public String[][] child = { { "A君", "B君", "C君", "D君" },
			{ "同学甲", "同学乙", "同学丙" }, { "御姐", "萝莉" } };
	public String[] parent = { "xxxx好友", "xxxx同学" };
	public String[][][] child_grandson = { { { "A君" }, { "AA", "AAA" } },
			{ { "B君" }, { "BBB", "BBBB", "BBBBB" } },
			{ { "C君" }, { "CCC", "CCCC" } },
			{ { "D君" }, { "DDD", "DDDD", "DDDDD" } }, };
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		this.setTitle("ExpandableListView练习----hellogv");
		btnNormal = (Button) this.findViewById(R.id.btnNormal);
		btnNormal.setOnClickListener(new ClickEvent());
		btnSuper = (Button) this.findViewById(R.id.btnSuper);
		btnSuper.setOnClickListener(new ClickEvent());
		adapter = new TreeViewAdapter(this, TreeViewAdapter.PaddingLeft >> 1);
		superAdapter = new SuperTreeViewAdapter(this, stvClickEvent);
		expandableList = (ExpandableListView) testExpandableList.this;
	}
}
```