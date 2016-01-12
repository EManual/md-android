配置文件如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@android:color/background_light"
    android:orientation="vertical"
    android:padding="0.0dip" >
    <ListView
        android:id="@+id/formcustomspinner_list"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_weight="1"
        android:cacheColorHint="@null"
        android:divider="@android:drawable/divider_horizontal_bright"
        android:scrollbars="vertical" />
    <Button
        android:id="@+id/formcustomspinner_btall"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="全部" />
</LinearLayout>
```
代码如下：
```  
public class CustomerSpinner extends Spinner implements OnItemClickListener {
	private AlertDialog dialog = null;
	private OnClickListener buttonClickListener = null;
	public CustomerSpinner(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public void setButtonClickListener(OnClickListener listener) {
		buttonClickListener = listener;
	}
	@Override
	public boolean performClick() {
		Context context = getContext();
		final DropDownAdapter adapter = new DropDownAdapter(getAdapter());
		final LayoutInflater inflater = LayoutInflater.from(getContext());
		final View view = inflater.inflate(R.layout.formcustomspinner, null);
		final ListView listview = (ListView) view.findViewById(R.id.formcustomspinner_list);
		listview.setAdapter(adapter);
		listview.setSelection(getSelectedItemPosition());
		listview.setOnItemClickListener(this);
		final Button btAll = (Button) view.findViewById(R.id.formcustomspinner_btall);
		btAll.setOnClickListener(buttonClickListener);
		AlertDialog.Builder builder = new AlertDialog.Builder(context);
		dialog = builder.setView(view).create();
		dialog.show();
		return true;
	}
	@Override
	public void onItemClick(AdapterView<?> view, View itemView, int position,
			long id) {
		setSelection(position);
		if (dialog != null) {
			dialog.dismiss();
			dialog = null;
		}
	}
	private static class DropDownAdapter implements ListAdapter, SpinnerAdapter {
		private SpinnerAdapter mAdapter;
		public DropDownAdapter(SpinnerAdapter adapter) {
			this.mAdapter = adapter;
		}
		public int getCount() {
			return mAdapter == null ? 0 : mAdapter.getCount();
		}
		public Object getItem(int position) {
			return mAdapter == null ? null : mAdapter.getItem(position);
		}
		public long getItemId(int position) {
			return mAdapter == null ? -1 : mAdapter.getItemId(position);
		}
		public View getView(int position, View convertView, ViewGroup parent) {
			return getDropDownView(position, convertView, parent);
		}
		public View getDropDownView(int position, View convertView,
				ViewGroup parent) {
			return mAdapter == null ? null : mAdapter.getDropDownView(position,
					convertView, parent);
		}
		public boolean hasStableIds() {
			return mAdapter != null && mAdapter.hasStableIds();
		}
		@Override
		public void registerDataSetObserver(DataSetObserver observer) {
			if (mAdapter != null) {
				mAdapter.registerDataSetObserver(observer);
			}
		}
		@Override
		public void unregisterDataSetObserver(DataSetObserver observer) {
			if (mAdapter != null) {
				mAdapter.unregisterDataSetObserver(observer);
			}
		}
		public boolean areAllItemsEnabled() {
			return true;
		}
		public boolean isEnabled(int position) {
			return true;
		}
		public int getItemViewType(int position) {
			return 0;
		}
		public int getViewTypeCount() {
			return 1;
		}
		public boolean isEmpty() {
			return getCount() == 0;
		}
	}
}
```

![img](https://emanual.github.io/md-android/img/view_spinner/04_spinner.jpg)  