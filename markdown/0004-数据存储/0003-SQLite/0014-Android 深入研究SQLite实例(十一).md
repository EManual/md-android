java代码：
```  
HashMap itemData = (HashMap)listView.getItemAtPosition(position);
String personid = itemData.get("personid");
String name = itemData.get("name");
String age = itemData.get("age");
//输出 01-17 14:54:47.919: INFO/DataActivity(9280):
//className=android.widget.RelativeLayout
Log.i(TAG, "className="+ view.getClass().getName());
Log.i(TAG, "personid="+ personid+ "name="+name + "age"+ age);
Log.i(TAG, "result="+ (position==id)); //trues
Log.i(TAG, "id="+id);
Log.i("TAG", "position="+position);
Toast.makeText(DataActivity.this, name.toString(),1).show();
}
[Tags]/**
[Tags]* 方法二 获取 值
[Tags]* 推荐使用方法二去设置适配器 获取数据的值 这样会更合理
[Tags]* 绑定的数据是游标形式 但是主键id 必须以_id命名 如果不是 可以在查询数据的时候设置别名 并且绑定的参数必须是_id
[Tags]* 否则会报异常信息
[Tags]*/
// Cursor cursor = personservice.getdateRawPerson(0, 10);
[Tags]/**
[Tags]* 参数一 上下文信息
[Tags]* 参数二 加载的视图界面文件
[Tags]* 参数三 游标数据
[Tags]* 参数四 数据目录
[Tags]* 参数五 对应的数据值对应的id
[Tags]* 表示把参数五对应的字段 参数四绑定起来
[Tags]* 注意 列名 必须指定为_id 如果你的数据的主键id 不是以_id命名 必须在查询的时候 指定 别名为_id 否则会报异常信息
[Tags]*/
// SimpleCursorAdapter ada = new SimpleCursorAdapter(this, R.layout.person, cursor,
// new String[]{"_id", "name", "age"}, new int[]{R.id.personid, R.id.name, R.id.age});
//绑定适配器
// listview.setAdapter(ada);
```
