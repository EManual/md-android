在ListView，GridView。。。。中经常用到适配器Adapter，但是anroid提供的Adapter只是几种框架，如果我们有需求，还是要自己根据需求而自定义Adapter的。
Android提供的三种Adapter主要有ArrayAdapter，SimpleAdapter，SimpleCursorAdapter，ArraAdapter是简单的字符串适配器（很丑，因为没办法帅。。。），SimpleAdapter是可以自定义子View布局的，可以有图片（只限于本地图片，如果要用网络加载图片，请参考我之前的一篇Blog），SimpleCursorAdapter主要用于数据库，前两个的数据来源一般都是String[]或者List，后一个的数据来源一般是数据库查询得到的Cursor
而我们自定义用的最多的还是继承自SimpleAdapter，下面说一下具体用法
我还是连着上一篇Blog，想做一个显示带下载进度条的子View显示于ListView中，SimpleAdapter可以显示一般的图片，但是无法显示进度条（因为不只是要显示，还要有实时更新），所以我们的做法是继承SimpleAdapter，具体要复写的方法有4个：
```  
public int getCount() 
public Object getItem(int position) 
public long getItemId(int position)
public View getView(int position, View convertView, ViewGroup parent)
```
还有一个更重要的是其构造方法MyAdapter(Context context, List<Map<String, Object>> list)，参数不是固定的，可以根据要用到的数据自己定义，第一个参数是要显示的上下文环境，第二个参数是用来记录各个条目的信息
第一个方法主要是返回ListView中要显示的子View数量，也就是下载任务数，只要返回构造方法中的list的条目就可以了
第二个方法是要返回一个子View，即ListView中的一个子条目，当然你也可以自定义返回你想要的信息
第三个方法是根据ListView中的位置返回id、
最重要最难理解的也就是第四个方法了，第四个方法主要是返回这个条目的整个信息，它是一个单独的布局文件，当然根据android结构也是一个View类的继承类了，这里还有一个知识点是LayoutInflater类，它的inflate()方法可以根据布局文件获得其View返回值，而最重要的思想是你要从这些条目中获得其子View（关系为ListView中有很多条目，每个条目中又有很多组件，我这里是ListView中多个下载任务是不同的条目，每个下载任务中的名字，进度又是其子View的组件），再得到其子组件之后，就可以根据构造方法中List<Map<String, Object>> list参数传递的值进行对应的赋值或者设置资源了，具体代码如下：
```  
import java.util.List;
import java.util.Map;
import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.LinearLayout;
import android.widget.ProgressBar;
import android.widget.TextView;
public class MyAdapter extends BaseAdapter {
	private Context context;
	private LayoutInflater layoutInflater;
	private List<Map<String, Object>> list;
	// 构造方法，参数list传递的就是这一组数据的信息
	public MyAdapter(Context context, List<Map<String, Object>> list) {
		this.context = context;
		layoutInflater = LayoutInflater.from(context);
		this.list = list;
	}
	// 得到总的数量
	public int getCount() {
		return this.list != null ? this.list.size() : 0;
	}
	// 根据ListView位置返回View
	public Object getItem(int position) {
		return this.list.get(position);
	}
	// 根据ListView位置得到List中的ID
	public long getItemId(int position) {
		return position;
	}
	// 根据位置得到View对象
	public View getView(int position, View convertView, ViewGroup parent) {
		if (convertView == null) {
			convertView = layoutInflater.inflate(R.layout.item, null);
		}
		// 得到条目中的子组件
		TextView tv1 = (TextView) convertView.findViewById(R.id.nameTextView);
		ProgressBar pb = (ProgressBar) convertView
				.findViewById(R.id.sizeProgressBar);
		TextView tv2 = (TextView) convertView.findViewById(R.id.sizeTextView);
		// 从list对象中为子组件赋值
		tv1.setText(list.get(position).get("name").toString());
		pb.setProgress(Integer.parseInt(list.get(position).get("size")
				.toString()));
		tv2.setText(list.get(position).get("size").toString());
		return convertView;
	}
}
```