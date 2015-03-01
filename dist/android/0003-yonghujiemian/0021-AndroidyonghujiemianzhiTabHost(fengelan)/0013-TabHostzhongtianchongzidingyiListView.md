在TabHost中利用ListView填充Tab标签的基础上，加上自定义ListView的代码，实现了如下效果：
![img](P)  
可以看到ListView背景色变成了白色，实现背景变成白色的代码片段如下：
```  
public class MyListView extends ListActivity {
	// private List data = new ArrayList();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		this.getListView().setBackgroundColor(android.graphics.Color.WHITE);
		this.getListView().setCacheColorHint(0);
		SimpleAdapter adapter = new SimpleAdapter(this, getData(),
				R.layout.mylist, new String[] { "title", "info", "img" },
				new int[] { R.id.title, R.id.info, R.id.img });
		setListAdapter(adapter);
	}
}
```
比较重要的代码是红色部分。第一行代码很好理解，就是设定背景色为白色。第二行代码的作用是保证在滑动ListView的时候背景不会变黑。如果不加第二行代码，滑动ListView时的效果会是这样：
![img](P)  