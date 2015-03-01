我们大家都会遇到这样的事，就是我们想给listview上分类，或者有时候想让listview一行显示两列内容或是三列内容的时候，怎么来实现那，现在我们就可以解决了，我们得给listview力添加多个adapter就可以了。我们现在就烂看看下面的代码是怎么实现的吧：
```  
class Section {
	String caption;
	Adapter adapter;

	Section(String caption, Adapter adapter) {
		this.caption = caption;
		this.adapter = adapter;
	}
}
// 自定义一个类，这个类呢包含多个adapter就可以了，想用那种就用那种。
abstract public class SectionedAdapter extends BaseAdapter {
	abstract protected View getHeaderView(String caption, int index,
			View convertView, ViewGroup parent);

	private List<Section> sections = new ArrayList<Section>();
	private static int TYPE_SECTION_HEADER = 0;

	public SectionedAdapter() {
		super();
	}

	public void addSection(String caption, Adapter adapter) {
		sections.add(new Section(caption, adapter));
	}

	public Object getItem(int position) {
		for (Section section : this.sections) {
			if (position == 0) {
				return (section);
			}
			int size = section.adapter.getCount() + 1;
			if (position < size) {
				return (section.adapter.getItem(position - 1));
			}
			position -= size;
		}
		return (null);
	}
	public int getCount() {
		int total = 0;
		for (Section section : this.sections) {
			total += section.adapter.getCount() + 1; // add one for header
		}
		return (total);
	}
	public int getViewTypeCount() {
		int total = 1; // one for the header, plus those from sections
		for (Section section : this.sections) {
			total += section.adapter.getViewTypeCount();
		}
		return (total);
	}
	public int getItemViewType(int position) {
		int typeOffset = TYPE_SECTION_HEADER + 1; // start counting from here
		for (Section section : this.sections) {
			if (position == 0) {
				return (TYPE_SECTION_HEADER);
			}
			int size = section.adapter.getCount() + 1;
			if (position < size) {
				return (typeOffset + section.adapter
						.getItemViewType(position - 1));
			}
			position -= size;
			typeOffset += section.adapter.getViewTypeCount();
		}
		return (-1);
	}
	public boolean areAllItemsSelectable() {
		return (false);
	}
	public boolean isEnabled(int position) {
		return (getItemViewType(position) != TYPE_SECTION_HEADER);
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		int sectionIndex = 0;
		for (Section section : this.sections) {
			if (position == 0) {
				return (getHeaderView(section.caption, sectionIndex,
						convertView, parent));
			}
			int size = section.adapter.getCount() + 1;
			if (position < size) {
				return (section.adapter.getView(position - 1, convertView,
						parent));
			}
			position -= size;
			sectionIndex++;
		}
		return (null);
	}
	@Override
	public long getItemId(int position) {
		return (position);
	}
	class Section {
		String caption;
		Adapter adapter;
		Section(String caption, Adapter adapter) {
			this.caption = caption;
			this.adapter = adapter;
		}
	}
}
public class SectionedDemo extends ListActivity {
	private static String[] items = { "lorem", "ipsum", "dolor", "purus" };
	@Override
	public void onCreate(Bundle icicle) {
		super.onCreate(icicle);
		setContentView(R.layout.main);
		adapter.addSection("Original", new ArrayAdapter<String>(this,
				android.R.layout.simple_list_item_1, items));
		List<String> list = Arrays.asList(items);
		Collections.shuffle(list);
		adapter.addSection("Shuffled", new ArrayAdapter<String>(this,
				android.R.layout.simple_list_item_1, list));
		list = Arrays.asList(items);
		Collections.shuffle(list);
		adapter.addSection("Re-shuffled", new ArrayAdapter<String>(this,
				android.R.layout.simple_list_item_1, list));
		setListAdapter(adapter);
	}
	SectionedAdapter adapter = new SectionedAdapter() {
		protected View getHeaderView(String caption, int index,
				View convertView, ViewGroup parent) {
			TextView result = (TextView) convertView;
			if (convertView == null) {
				result = (TextView) getLayoutInflater().inflate(
						R.layout.header, null);
			}
			result.setText(caption);
			return (result);
		}
	};
}
```