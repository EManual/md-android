很多客户端软件和浏览器软件都喜欢用Tab分页标签来搭建界面框架。读者也许会马上想到使用TabHost与TabActivity的组合，其实最常用的不是它们，而是由GridView与ActivityGroup的组合。每当用户在GridView选中一项，ActivityGroup就把该项对应的Activity的Window作为View添加到ActivityGroup所指定的容器(LinearLayout)中。
ImageAdapter是本实例的关键之一，它继承于BaseAdapter，并加入一些自定义的方法。ImageAdapter的源码如下：
```  
import android.content.Context;  
import android.graphics.drawable.ColorDrawable;  
import android.view.View;  
import android.view.ViewGroup;  
import android.widget.BaseAdapter;  
import android.widget.GridView;  
import android.widget.ImageView;  
public class ImageAdapter extends BaseAdapter {  
    private Context mContext;   
    private ImageView[] imgItems;  
    private int selResId;  
    public ImageAdapter(Context c,int[] picIds,int width,int height,int selResId) {   
        mContext = c;
        this.selResId=selResId;  
        imgItems=new ImageView[picIds.length];
		for (int i = 0; i < picIds.length; i++)  
        {
            imgItems[i] = new ImageView(mContext);
            imgItems[i].setLayoutParams(new GridView.LayoutParams(width, height));//设置ImageView宽高
            imgItems[i].setAdjustViewBounds(false);
            //imgItems[i].setScaleType(ImageView.ScaleType.CENTER_CROP);   
            imgItems[i].setPadding(2, 2, 2, 2);
            imgItems[i].setImageResource(picIds[i]);
        }  
    }   
    public int getCount() {   
        return imgItems.length;   
    }   
    public Object getItem(int position) {   
        return position;   
    }   
    public long getItemId(int position) {   
        return position;   
    }   
     /**  
      * 设置选中的效果  
      */    
    public void SetFocus(int index)    
    {
        for(int i=0;i<imgItems.length;i++)
        {
            if(i!=index)
            {
                imgItems[i].setBackgroundResource(0);//恢复未选中的样式
            }    
        }    
        imgItems[index].setBackgroundResource(selResId);//设置选中的样式
    }    
    public View getView(int position, View convertView, ViewGroup parent) {   
        ImageView imageView;
        if (convertView == null) {   
            imageView=imgItems[position];
        } else {   
            imageView = (ImageView) convertView;
        }   
        return imageView;   
    }   
}  
```
SetFocus(int)这个方法是个关键点，即实现选中的效果。例如有ABCD4个Item，其中C被选中了，那么除C以外的Item都被设置为未被选中的样式，而C则设置为选中的样式。
接下来就开始写主Activity，主Activity包含GridView控件，名为gvTopBar，有2点是需要注意一下的。
SetNumColumns()：必须要使用setNumColumns来设置列数，因为这个GridView只有一行，即所有的Item都在同一行，Item数量即为列数。
setSelector(new ColorDrawable(Color.TRANSPARENT))：把系统默认选中的背景色透明化，因为我们已经在BaseAdapter中加入了SetFocus()来改变选中的样式。 
```  
import android.app.Activity;
import android.app.ActivityGroup;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.graphics.Color;
import android.graphics.drawable.ColorDrawable;
import android.os.Bundle;
import android.util.Log;
import android.view.Gravity;
import android.view.View;
import android.view.Window;
import android.view.ViewGroup.LayoutParams;
import android.widget.AdapterView;
import android.widget.GridView;
import android.widget.LinearLayout;
import android.widget.Toast;
import android.widget.AdapterView.OnItemClickListener;

public class ActivityGroupDemo extends ActivityGroup {
	private GridView gvTopBar;
	private ImageAdapter topImgAdapter;
	public LinearLayout container;// 装载sub Activity的容器
	 /** 顶部按钮图片 **/
	int[] topbar_image_array = { R.drawable.topbar_home,
			R.drawable.topbar_user, R.drawable.topbar_shoppingcart,
			R.drawable.topbar_note };
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		gvTopBar = (GridView) this.findViewById(R.id.gvTopBar);
		gvTopBar.setNumColumns(topbar_image_array.length);// 设置每行列数
		gvTopBar.setSelector(new ColorDrawable(Color.TRANSPARENT));// 选中的时候为透明色
		gvTopBar.setGravity(Gravity.CENTER);// 位置居中
		gvTopBar.setVerticalSpacing(0);// 垂直间隔
		int width = this.getWindowManager().getDefaultDisplay().getWidth()
				/ topbar_image_array.length;
		topImgAdapter = new ImageAdapter(this, topbar_image_array, width, 48,
				R.drawable.topbar_itemselector);
		gvTopBar.setAdapter(topImgAdapter);// 设置菜单Adapter
		gvTopBar.setOnItemClickListener(new ItemClickEvent());// 项目点击事件
		container = (LinearLayout) findViewById(R.id.Container);
		SwitchActivity(0);// 默认打开第0页
	}
	class ItemClickEvent implements OnItemClickListener {
		public void onItemClick(AdapterView<?> arg0, View arg1, int arg2,
				long arg3) {
			SwitchActivity(arg2);
		}
	}
	 /**
	  * 根据ID打开指定的Activity
	  * @param id
	  *            GridView选中项的序号
	  */
	void SwitchActivity(int id) {
		topImgAdapter.SetFocus(id);// 选中项获得高亮
		container.removeAllViews();// 必须先清除容器中所有的View
		Intent intent = null;
		if (id == 0 || id == 2) {
			intent = new Intent(ActivityGroupDemo.this, ActivityA.class);
		} else if (id == 1 || id == 3) {
			intent = new Intent(ActivityGroupDemo.this, ActivityB.class);
		}
		intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
		// Activity 转为 View
		Window subActivity = getLocalActivityManager().startActivity(
				"subActivity", intent);
		// 容器添加View
		container.addView(subActivity.getDecorView(), LayoutParams.FILL_PARENT,
				LayoutParams.FILL_PARENT);
	}
}
```
主Activity的布局XML文件源码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <RelativeLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
        <GridView
            android:id="@+id/gvTopBar"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentTop="true
            android:fadingEdge="vertical"
            android:fadingEdgeLength="5dip" >
        </GridView>
        <LinearLayout
            android:id="@+id/Container"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:layout_below="@+id/gvTopBar" >
        </LinearLayout>
    </RelativeLayout>
</LinearLayout>
```