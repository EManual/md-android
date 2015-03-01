代码如下：
```  
import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;
import android.widget.TextView;
public class GridAdapter extends BaseAdapter {
	private Context context;
	private String[] itemString;
	private int[] itemIcons;
	public GridAdapter(Context con, String[] itemString, int[] itemIcons) {
		context = con;
		this.itemString = itemString;
		this.itemIcons = itemIcons;
	}
	@Override
	public int getCount() {
		return itemIcons.length;
	}
	@Override
	public Object getItem(int position) {
		return itemString[position];
	}
	@Override
	public long getItemId(int position) {
		return position;
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		LayoutInflater inflater = LayoutInflater.from(context);
		/* 使用item.xml为每几个item的Layout */
		View v = inflater.inflate(R.layout.item, null);
		/* 取得View */
		ImageView iv = (ImageView) v.findViewById(R.id.item_grid);
		TextView tv = (TextView) v.findViewById(R.id.item_text);
		/* 设定显示的Image与文? */
		iv.setImageResource(itemIcons[position]);
		tv.setText(itemString[position]);
		return v;
	}
} 
```
还有注意的是：
SlidingDrawer should be used as an overlay inside layouts. This means SlidingDrawer should only be used inside of a FrameLayout or a RelativeLayout for instance,如果显示的时候不正常，考虑上面的原因。
