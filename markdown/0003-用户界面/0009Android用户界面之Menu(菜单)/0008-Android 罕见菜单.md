创建上下文菜单
第一种方式是为View类创建通用的上下文菜单，通过重写View的onCreateContextMenu处理函数，如下：
```  
@Override
public void onCreateContextMenu(ContextMenu menu) {
	super.onCreateContextMenu(menu);
	menu.add("ContextMenuItem1");
}
```
这种方式创建的上下文菜单会在任何包含View类的Activity中获得。
更加变通的方式是创建Activity指定的上下文菜单，通过重写onCreateContextMenu方法并注册需要上下文菜单的View。注册View使用registerForContextMenu方法，传入View，如下代码所示：
```  
@Override
public void onCreate(Bundle icicle) {
	super.onCreate(icicle);
	EditText view = new EditText(this);
	setContentView(view);
	registerForContextMenu(view);
}
```
一旦View被注册，任何上下文菜单该显示的时候，onCreateContextMenu处理函数都会被触发。
重写onCreateContextMenu方法，并检查哪个View触发菜单创建，并填入合适的菜单项，如下面的代码片段所示：
```  
@Override
public void onCreateContextMenu(ContextMenu menu, View v,
		ContextMenu.ContextMenuInfo menuInfo) {
	super.onCreateContextMenu(menu, v, menuInfo);
	menu.setHeaderTitle("Context Menu");
	menu.add(0, menu.FIRST, Menu.NONE, "Item 1").setIcon(
			R.drawable.menu_item);
	menu.add(0, menu.FIRST + 1, Menu.NONE, "Item 2").setCheckable(true);
	menu.add(0, menu.FIRST + 2, Menu.NONE, "Item 3").setShortcut('3', '3');
	SubMenu sub = menu.addSubMenu("Submenu");
	sub.add("Submenu Item");
}
```
如上所示，ContextMenu类支持和Menu类相同的add方法，所以，你可以用和Activity的Menu相同的方法填入上下文菜单项——包括子菜单，但它们俩都不支持图标。你也可以指定上下文菜单头条的文本和图标。
Android支持使用Intent Filter运行时填入上下文菜单。这个机制允许你指定当前的View呈现的数据类型和询问其它Android应用程序是否支持一些行为来填入上下文菜单。这个行为最普通的例子是EditText控件上的剪切、拷贝、粘贴菜单项。这些内容将在下一章详细描述。
处理上下文菜单选择
上下文菜单项选择的处理和Activity的菜单相似。你可以附加一个Intent或者菜单项Click Listener到每个菜单项上，或者通过重写Activity中的onContextItemSelected这个首选技巧。
在Activity里，什么时候上下文菜单项被选择，这个事件处理函数就被触发。一个框架实现如下所示：
```  
@Override
public boolean onContextItemSelected(MenuItem item) {
	super.onContextItemSelected(item);
	return false;
}
```