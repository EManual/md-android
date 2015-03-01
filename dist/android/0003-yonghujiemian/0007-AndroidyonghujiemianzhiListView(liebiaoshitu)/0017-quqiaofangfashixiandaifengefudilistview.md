之前想做个带分隔符的listview（以前写的listview另类解法就是为了实现分隔符但是不完美），现在借鉴别人的程序做了一个比较理想的例子发出来大家围观一记，上个图吧：
![img](P)  
下面奉上程序：
main.xml是这个样子的：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ListView
        android:id="@+id/lview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:scrollbars="none" >
    </ListView>
</LinearLayout>
```
info.xml是这个样子的：
```  
<?xml version="1.0" encoding="UTF-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/tview"
        android:layout_width="fill_parent"
        android:layout_height="0dp"
        android:background="ff111111"
        android:textColor="ffaaaaaa" >
    </TextView>
    <RelativeLayout
        android:id="@+id/rlayout"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:descendantFocusability="blocksDescendants" >
        <ImageButton
            android:id="@+id/img"
            android:layout_width="40dp"
            android:layout_height="23dp"
            android:layout_alignParentLeft="true"
            android:layout_centerVertical="true"
            android:background="00ffffff"
            android:paddingLeft="3pt" >
        </ImageButton>
        <TextView
            android:id="@+id/tview01"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_toRightOf="@id/img"
            android:paddingLeft="3pt"
            android:textColor="ffffffff"
            android:textSize="16sp" >
        </TextView>
        <TextView
            android:id="@+id/tview02"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/tview01"
            android:layout_toRightOf="@id/img"
            android:paddingLeft="3pt"
            android:textColor="ffffffff"
            android:textSize="12sp" >
        </TextView>
    </RelativeLayout>
</LinearLayout>
```
虽然info.xml中放了一个img，但是我没用到，只是放着看的，大家要用的时候自己加东西就行了
然后是java代码了：
```  
public class Text extends Activity {
    List< String[] > list;
    String[] str0 = { "a", "aaaaa" };
    String[] str1 = { "a", "aaaaa" };
    String[] str2 = { "b", "bbbbb" };
    String[] str3 = { "c", "ccccc" };
    String[] str4 = { "c", "ccccc" };
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        list = new ArrayList< String[] >();
        list.add(str0);
        list.add(str1);
        list.add(str2);
        list.add(str3);
        list.add(str4);
        ListView lview = (ListView) findViewById(R.id.lview);
        Adapter adapter = new Adapter(this, list);
        lview.setAdapter(adapter);
    }
    class Adapter extends BaseAdapter {
        private LayoutInflater inflater;
        String str = null;
        List< String[] > list;
        public Adapter(Context c, List< String[] > list) {
            this.inflater = LayoutInflater.from(c);
            this.list = list;
        }
        @Override
        public int getCount() {
            return list.size();
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
            View view = inflater.inflate(R.layout.info, null);
            if (null == str || !str.equals(list.get(position)[0])) {
                str=list.get(position)[0];
                LinearLayout.LayoutParams lp =new LinearLayout.LayoutParams(LayoutParams.FILL_PARENT, 20);
                TextView tview = (TextView) view.findViewById(R.id.tview);
                tview.setLayoutParams(lp);
                tview.setText(list.get(position)[0]);
            }
            TextView tview01 = (TextView) view.findViewById(R.id.tview01);
            tview01.setText(list.get(position)[0]);
            TextView tview02 = (TextView) view.findViewById(R.id.tview02);
            tview02.setText(list.get(position)[1]);
            return view;
        }
    }
}
```
超简单，各位有建议提提，让我这个程序更简单些。