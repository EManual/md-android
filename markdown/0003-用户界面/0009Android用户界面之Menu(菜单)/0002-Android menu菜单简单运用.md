为程序添加Menu菜单
```  
// 创建OptionsMenu publi c bool ean onCreateOpti onsMenu(Menu menu)
// 处理选择事件publi c bool ean onOpti onsIt emSel ect ed(MenuIt em i t em)
public boolean onCreateOptionsMenu(Menu menu) {
	boolean result = super.onCreateOptionsMenu(menu);
	menu.add(0, INSERT_ID_Play, 0, R.string.menu_toPlay);
	menu.add(0, INSERT_ID_Stop, 0, R.string.menu_toStop);
	return result;
}
// 创建菜单
public boolean onOptionsItemSelected(MenuItem item) {
	if(item.getItemId()==INSERT_ID_Play){
	Play_Music();
	} if if(item.getItemId()==INSERT_ID_Stop){
			Stop_Music();
	 } return super.onOptionsItemSelected(item);
}
```