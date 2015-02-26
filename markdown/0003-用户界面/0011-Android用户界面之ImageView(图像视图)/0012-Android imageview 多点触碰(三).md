Activity
```  
import Android.app.Activity;
import Android.os.Bundle;
import Android.view.View;
import Android.widget.AdapterView;
import Android.widget.Gallery;
import Android.widget.LinearLayout;
import Android.widget.AdapterView.OnItemClickListener;
 /**
  * activity
  */
public class GalleryMain extends Activity implements OnItemClickListener {
	private ViewScroll detail;
	private ImageAdapter ia;
	private LinearLayout ll;
	private LinearLayout.LayoutParams parm;
	private Gallery g;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		g = (Gallery) findViewById(R.id.myggg);
		ll = (LinearLayout) findViewById(R.id.twill);
		parm = new LinearLayout.LayoutParams(
				LinearLayout.LayoutParams.FILL_PARENT,
				LinearLayout.LayoutParams.FILL_PARENT);
		ia = new ImageAdapter(this);
		detail = new ViewScroll(GalleryMain.this, ia.imgIds[0], g);
		ll.addView(detail, parm);
		g.setAdapter(ia);
		g.setOnItemClickListener(this);
	}
	@Override
	public void onItemClick(AdapterView<?> arg0, View arg1, int arg2, long arg3) {
		ll.removeView(detail);
		detail = new ViewScroll(GalleryMain.this, ia.imgIds[arg2], g);
		ll.addView(detail, parm);
	}
}
```
配合gallery的适配器类
```  
import Android.content.Context;
import Android.view.View;
import Android.view.ViewGroup;
import Android.widget.BaseAdapter;
import Android.widget.Gallery;
import Android.widget.ImageView;
import Android.widget.ImageView.ScaleType;
 /**
  * Gallery的适配器类
  */
public class ImageAdapter extends BaseAdapter {
	/* 图片素材 */
	public int[] imgIds = { R.drawable.jpg, R.drawable.pic };
	private Context context;
	public ImageAdapter(Context context) {
		this.context = context;
	}
	@Override
	public int getCount() {
		return imgIds.length;
	}
	@Override
	public Object getItem(int position) {
		return null;
	}
	@Override
	public long getItemId(int position) {
		return 0;
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		ImageView img = new ImageViewImp(context);
		img.setImageResource(imgIds[position]);
		img.setScaleType(ScaleType.CENTER);
		img.setLayoutParams(new Gallery.LayoutParams(155, 150));
		return img;
	}
}
```