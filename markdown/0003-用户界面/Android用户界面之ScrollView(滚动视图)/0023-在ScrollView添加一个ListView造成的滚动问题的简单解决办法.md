正常来说，在ScrollView添加一个ListView后在真机上只会显示ListView的一行多一点，我也不理解为什么会这样，后来我把ListView的layout_height改成400dip，而不是用match_parent和wrap_content，我发现这样的话ListView就显示的多了很多。所以就产生了把ListView所有的item的高度算出来给ListView设置的想法。下面是代码： 
```  
public void setListViewHeightBasedOnChildren(ListView listView) {
	ListAdapter listAdapter = listView.getAdapter();
	if (listAdapter == null) {
		return;
	}
	int totalHeight = 0;
	for (int i = 0; i < listAdapter.getCount(); i++) {
		View listItem = listAdapter.getView(i, null, listView);
		listItem.measure(0, 0);
		totalHeight += listItem.getMeasuredHeight();
	}
	ViewGroup.LayoutParams params = listView.getLayoutParams();
	params.height = totalHeight
			+ (listView.getDividerHeight() * (listAdapter.getCount() - 1));
	params.height += 5;// if without this statement,the listview will be a
						// little short
	listView.setLayoutParams(params);
}
```
在代码的倒数第二行二我又给加了5个像素，这是因为我在listview的属性里面添加了padding=5dip。 
然后每次ListView的数据一有变化就用这个函数设置一下就好了，不过这样总感觉效率很低，希望有达人给指点一下。 
简单来说就是把layout_height写死，这种办法也很适用于GridView（如果能估计得出GridView的高度的话）。 
listview与ScrollView老问题的另类解法 
这几天一直被listview怎么合理的放进scorllview中的问题困扰，尝试过把listview放入scorllview中的朋友都知道，被放入的listview显示是有问题的，无论怎么设置layout都只显示大概2行的高度，看起来很郁闷，更别说美观了，后来上网查询了一下，解决方法有的是用linearlayout替换listview，还有修改onmeasure的，我比较懒个人感觉很麻烦不喜欢，终于想出了一个还算和谐的解决方法：xml中的textlist设置如下： 
```  
<?xml version="1.0" encoding="UTF-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:background="44444444"
    android:orientation="vertical" >
    <ScrollView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
        <LinearLayout
            android:id="@+id/ll1"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:background="ff888888"
            android:orientation="vertical"
            android:paddingBottom="30dp"
            android:paddingLeft="15dp"
            android:paddingRight="15dp"
            android:paddingTop="30dp"
            android:scrollbars="vertical" >
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:text="あ"
                android:textColor="ffeeeeee"
                android:textSize="18sp" >
            </TextView>
            <ListView
                android:id="@+id/lv0"
                android:layout_width="fill_parent"
                android:layout_height="20dp"
                android:scrollbars="none"
                android:stackFromBottom="true" >
            </ListView>
        </LinearLayout>
    </ScrollView>
</LinearLayout>
```
其中的textview是我做的东西要用到的，和方法无关可以不看，然后就是在java中重新设置listview的高度了，目的是把listview“撑”开： 
```  
LinearLayout.LayoutParams lp5 = new LinearLayout.LayoutParam(LayoutParams.FILL_PARENT, listItem.size()*51-1); 
```
其中第一个属性不必说了，第二个是为了计算listview要设置的总高度用的，51是我事先设置好的一行的高度（50）+每行之间的间隔（1）而得来的，listItem.size()是我要显示的行数，用.setLayoutParams(lp5);来重新设置高度，其他别的设置跟以前一样