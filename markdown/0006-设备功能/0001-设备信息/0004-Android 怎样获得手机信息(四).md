1.2.4 查看软件信息
在Android上，可以在手机上随便安装自己喜欢的应用软件，查看软件信息的功能就是收集并显示已经安装的应用软件信息。Android提供了getPackageManager()、getInstalledApplications(0)方法，可以直接返回全部已经安装的应用列表。这个功能就是只需要获取列表，再进行显示在列表中就可以了。但是，如果安装的软件比较多，那么获取信息所花费的时间会比较多，为了更好地完善用户使用的体验，在获取列表时，需要在界面提示用户耐心等待，这就需要用到Android提供的另外一个功能Runnable。
引入Runnable比较简单，只需要在定义类的时候实现Runnable接口就可以了，所以，这里的软件信息查看界面对应的Software.java类声明代码如下：
```  
public class Software extends Activity implements Runnable {	
}
```
然后需要在这个Activity启动的时候，引入进度条ProgressDialog来显示一个提示界面，onCreate代码如下所示：
```  
public void onCreate(Bundle icicle) {
	Super.onCreate(icicle);
	setContentView(R.layout.softwares);
	setTitle("软件信息");
	itemlist = (ListView) findViewById(R.id.itemlist);
	pd = ProgressDialog.show(this, "请稍候. .", "正在收集你已经安装的软件信息. . .", true,
			false);
	Thread thread = new Thread(this);
	thread.start();
}
```
该方法创建了一个ProgressDialog，并设定其提示信息。然后实现其线程的run()方法，该方法实现其真正执行的逻辑，实现代码如下：
```  
@Override
public void run() {
	fetch_installed_apps();
	handler.sendEmptyMessage(0);
}
```
上述代码调用自定义的fetch_installed_app()方法获取已经安装的应用信息，这个方法是比较消耗时间的，实现代码如下：
```  
public ArrayList fetch_installed_apps() {
	List<ApplicationInfo> packages = getPackageManager()
			.getInstalledApplications(0);
	ArrayList<HashMap<String, Object>> list = new ArrayList<HashMap<String, Object>>(
			packages.size());
	Iterator<ApplicationInfo> l = packages.iterator();
	while (l.hasNext()) {
		HashMap<String, Object> map = new HashMap<String, Object>();
		ApplicationInfo app = (ApplicationInfo) l.next();
		String packageName = app.packageName;
		String label = " ";
		try {
			label = getPackageManager().getApplicationLabel(app).toString();
		} catch (Exception e) {
			Log.i("Exception", e.toString());
		}
		map = new HashMap<String, Object>();
		map.put("name", label);
		map.put("desc", packageName);
		list.add(map);
	}
	return list;
}
```
上述代码使用getPackageManager().getInstalledApplications(0)获取已经安装的软件信息，进而构造用来显示的列表(List)对象，同时，界面通过进度条(ProgressDialog)显示提示信息。
当这个方法运行完成后，会调用handler.sendEmptyMessage(0)语句给handler发送一个通知消息，使其执行下面的动作，下面就是这个handler的实现方法：
```  
private Handler handler = new Handler() {
	public void handle(Message msg) {
		refreshListItems();
		pd.dismiss();
	}; 
}
```
上述代码中，当其接收到run()线程传递的消失后，先调用refreshListItems()方法显示列表，最后调用进度条ProgressDialog的dismiss方法使其等待提示消失。而refreshListItems()的实现代码如下：
```  
private void refreshListItems() {
	list = fetch_installed_apps();
	SimpleAdapter notes = new SimpleAdater(this, list, R.layout.info_row,
			new String[] { "name", "desc" }, new int[] { R.id.name,
					R.id.desc });
	list.setAdapter(notes);
	setTitle("软件信息，已经安装" + list.size() + "款应用.");
}
```
 这些代码，显示已经安装的应用列表的同时，在Title上显示一共安装了多少款应用。