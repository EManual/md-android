对于游戏中的数据进行保存方式，在Android中常用的有四种保存方式，这里我先给大家统一先简单的介绍下：
#### 1.SharedPreference
此保存方式试用于简单数据的保存，文如其名属于配置性质的保存，不适合数据比较大的保存方式；
#### 2.文件存储 (FIleInputStream/FileOutputStream)
此保存方式比较适合游戏的保存和使用，可以保存较大的数据，因为相对于SQLite来说更容易让童鞋们接受，此方式不仅能把数据存储在系统中也能将数据保存到SDcard中;
#### 3.SQLite
此保存方式比较适合游戏的保存和使用，可以保存较大的数据，并且可以将自己的数据存储到文件系统或者数据库当中，也可以将自己的数据存储到SQLite数据库当中,也能将数据保存到SDcard中;
#### 4.ContentProvider (不推荐用于游戏保存)
此保存方式不推荐用于游戏保存，因为此方式不仅能存储较大数据，还支持多个程序之间就的数据进行交换！！！但是由于游戏中基本就不可能去访问外部应用的数据,所以对于此方式我不予讲解， 有兴趣的可以去自行百度 google 学习;
以上简单的对几种常用的保存方式进行的概述，那么，下面会详细的去分析每个的优缺点以及每种保存的实现和需要注意的地方！
下面我首先向大家介绍第一种保存方式：
保存方式之：《SharedPreference》
优点：简单、方便、适合简单数据的快速保存
缺点：1.存数的文件只能在同一包内使用，不能在不同包之间使用!
2.默认将数据存放在系统路径下 /data/data/com.android/  ，没有找到放SD卡上的方法。
总结：其实本保存方式如同它的名字一样是个配置保存，虽然方便，但只适合存储比较简单的数据!
下面是SharedPreference 的代码实现和详细讲解:
```  
 /**
  * @author android
  * @保存方式：SharedPreference
  * @注意：SharedPreference 只能在同一包内使用，不能在不同包之间使用!
  * @操作模式: Context.MODE_PRIVATE：新内容覆盖原内容 Context.MODE_APPEND：新内容追加到原内容后
  *        Context.MODE_WORLD_READABLE：允许其他应用程序读取
  *        Context.MODE_WORLD_WRITEABLE：允许其他应用程序写入，会覆盖原数据。
  */
public class MainActivity extends Activity implements OnClickListener {
	private EditText et_login, et_password;
	private Button btn_save;
	private TextView tv_title;
	private SharedPreferences sp;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.main);
		btn_save = (Button) findViewById(R.id.button_save);
		btn_save.setOnClickListener(this);
		et_login = (EditText) findViewById(R.id.editText_Login);
		et_password = (EditText) findViewById(R.id.editText_Password);
		tv_title = (TextView) findViewById(R.id.tv_title);
		// 这里我们先调用 getSharedPreferences()来实例化一个SharedPreferences,
		// 第二个参数是指：操作模式(上面对各种操作模式已有解释)
		sp = getSharedPreferences("Setting_android", MODE_PRIVATE);
		/*
		  * 下面代码是我们要在程序刚启动的时候我们来读取之前的数据, 当然我们还没有保存任何数据所以肯定找不到！！
		  * 如果找不到也没关系会默认返回一个参数值，看下面的方法含义便知！
		  */
		sp.getString("login", "");
		// getString()类似哈希表，一个key 一个volue ,
		// 这个方法如果找不到对应的第一个参数(key),那么将以第二个参数作为此key的返回值
		et_login.setText(sp.getString("login", ""));
		et_password.setText(sp.getString("password", ""));
	}
	@Override
	public void onClick(View v) {
		if (v == btn_save) {
			if (et_login.getText().toString().equals(""))
				tv_title.setText("请输入帐号!");
			else if (et_password.getText().toString().equals(""))
				tv_title.setText("请输入密码!");
			else {
				sp.edit()
						.putString("login", et_login.getText().toString())
						.putString("password", et_password.getText().toString())
						.commit();
				// 从sp.edit()开始进入编辑状态，直到commit()提交！
				tv_title.setText("保存成功!可重新打开此程序,测试是否已经保存数据!
						+ "\n(或者在'File Explorer'窗口下-data-data-com.android路径下
						+ "是否存在" + "了'Setting_android.xml')");
			}
		}
	}
}
```
代码中的注释的很清楚了，比较简单,不多说了。             
保存方式之：《文件存储 OutputStream/InputStream》           
优点： 
1.适合游戏存储，能存储较大数据；  
2.不仅能存储到系统中，也能存储到SD卡中！           
总结：如果童鞋们对SQL不太熟习的话那么选择此种方式最为合适的啦。
```  
 /**
  * @author android
  * @保存方式：Stream 数据流方式
  * @注意1：默认情况下，使用openFileOutput 方法创建的文件只能被其调用的应用使用， 其他应用无法读取这个文件，如果需要在不同的应用中共享数据；
  * @注意2：因为android os内部闪存有限，所以适合保存较少的数据，当然我们也有解决的方法，
  *                就是把数据保存在SD开中，这样就可以了，后面我也会向大家讲解 !
  * @提醒1 调用FileOutputStream 时指定的文件不存在，Android 会自动创建它。 另外，在默认情况下，写入的时候会覆盖原
  *      文件内容，如果想把新写入的内 容附加到原文件内容后，则可以指定其mode为Context.MODE_APPEND。
  * @提醒2 启动程序就初始化的时候一定要注意处理！代码中有注释！一定要仔细看！
  * 
  * @提醒3 这里我给大家讲两种方式，一种是原生态file流来写入/读入, 另外一种是用Data流包装file流进行写入/读入
  *      其实用data流来包装进行操作; 原因是：包装后支持了更多的写入/读入操作，比如：file流写入不支持 writeUTF(String
  *      str); 但是用Data包装后就会支持。
  * @操作模式: Context.MODE_PRIVATE：新内容覆盖原内容 Context.MODE_APPEND：新内容追加到原内容后
  *        Context.MODE_WORLD_READABLE：允许其他应用程序读取
  *        Context.MODE_WORLD_WRITEABLE：允许其他应用程序写入，会覆盖原数据。
  */
public class MainActivity extends Activity implements OnClickListener {
	private EditText et_login, et_password;
	private Button btn_save;
	private TextView tv_title;
	private FileOutputStream fos;
	private FileInputStream fis;
	private DataOutputStream dos;
	private DataInputStream dis;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		String temp = null;
		super.onCreate(savedInstanceState);
		getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
				WindowManager.LayoutParams.FLAG_FULLSCREEN);
		this.requestWindowFeature(Window.FEATURE_NO_TITLE);
		setContentView(R.layout.main);
		btn_save = (Button) findViewById(R.id.button_save);
		btn_save.setOnClickListener(this);
		et_login = (EditText) findViewById(R.id.editText_Login);
		et_password = (EditText) findViewById(R.id.editText_Password);
		tv_title = (TextView) findViewById(R.id.tv_title);
		try {
			// openFileInput 不像 sharedPreference 中
			// getSharedPreferences的方法那样找不到会返回默认值,
			// 这里找不到数据文件就会报异常,所以finally里关闭流尤为重要!!!
			if (this.openFileInput("save.android") != null) {
				// --------------单纯用file来读入的方式-----------------
				// fis = this.openFileInput("save.android");
				// ByteArrayOutputStream byteArray = new
				// ByteArrayOutputStream();
				// byte[] buffer = new byte[1024];
				// int len = 0;
				// while ((len = fis.read(buffer)) > 0) {
				// byteArray.write(buffer, 0, len);
				// }
				// temp = byteArray.toString();
				// -------------- 用data流包装后的读入的方式------------
				fis = this.openFileInput("save.android");// 备注1
				dis = new DataInputStream(fis);
				et_login.setText(dis.readUTF());
				et_password.setText(dis.readUTF());
				// 这里也是在刚启动程序的时候去读入存储的数据
				// 读的时候要注意顺序; 例如我们写入数据的时候
				// 先写的字符串类型,我们也要先读取字符串类型,一一对应！
			}
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			// 在finally中关闭流!因为如果找不到数据就会异常我们也能对其进行关闭操作 ;
			try {
				if (this.openFileInput("save.android") != null) {
					// 这里也要判断，因为找不到的情况下，两种流也不会实例化。
					// 既然没有实例化，还去调用close关闭它,肯定"空指针"异常！！！
					fis.close();
				}
			} catch (FileNotFoundException e) {
				e.printStackTrace();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
	@Override
	public void onClick(View v) {
		if (Environment.getExternalStorageState() != null) {
			// 这个方法在试探终端是否有sdcard!
			Log.v("android", "有SD卡");
		}
		if (v == btn_save) {
			if (et_login.getText().toString().equals(""))
				tv_title.setText("请输入帐号!");
			else if (et_password.getText().toString().equals(""))
				tv_title.setText("请输入密码!");
			else {
				try {
					// ------单纯用file来写入的方式--------------
					// fos = new FileOutputStream(f);
					// fos.write(et_login.getText().toString().getBytes());
					// fos.write(et_password.getText().toString().getBytes());
					// ------data包装后来写入的方式--------------
					fos = this.openFileOutput("save.android", MODE_PRIVATE);// 备注2
					dos = new DataOutputStream(fos);
					dos.writeUTF(et_login.getText().toString());
					dos.writeUTF(et_password.getText().toString());
					tv_title.setText("保存成功!可重新打开此程序,测试是
							+ "否已经保存数据!\n(或者在'File Explorer
							+ "窗口下-data-data-com.android-files路径下
							+ "是否存在了'save.android')");
				} catch (FileNotFoundException e) {
					e.printStackTrace();
				} catch (IOException e) {
					e.printStackTrace();
				} finally {
					// 在finally中关闭流 这样即使try中有异常我们也能对其进行关闭操作 ;
					try {
						dos.close();
						fos.close();
					} catch (IOException e) {
						e.printStackTrace();
					}
				}
			}
		}
	}
}
```
以上代码中实现了两种流形式来完成写入和读入，这里我们为什么要使用Data流来包装，其实不光是获得更多的操作方式，最主要的是方便快捷，你比如用file来读入的时候，明显的复杂了一些不说，它还一次性把所有数据都取出来了，不便于对数据的处理！
强调的有几点：
1：在一开始对数据的访问再次提醒童鞋们，这个跟sharedPreference的获取方式不一样，sharedPreference的获取方式可以得到一个默认的值，但是你用咱们获取的是个文件，而且直接就去open这个文件，一旦不存在必定异常，所以这一块的异常处理，以及finally的处理一定要处理得当。
2.其实在一开始用data包装的时候发现写入的字符串在读入的时候发现字符乱码了，查了api才发现，api规定当写入字符串的时候必须写入UTF-8格式的编码，但是后来不知道怎么了就没事了。所以这里如果童鞋们遇到此问题，我给出大家一个解决方法，就是在写入的时候我们不要去DataOutputStream来包装而是用，OutputStreamWriter，因为在构造的可以设定编码！  
```                                 
OutputStreamWriter osw = new OutputStreamWriter(fis,"UTF-8"); 
String  content = EncodingUtils.getString(buffer, "UTF-8");   
```
这个也能把字符数组转码制！这样写入的就肯定是UTF-8编码的字符啦、下面介绍如何把我们的数据通过OutputStream/InputStream存入SD卡中!其实将我们的数据放入SD卡中，无疑就需要对代码进行两处的修改：注意：一定要有SD卡！对于如何创建SD卡在前一篇文章中已经说了两种方式，不会的童鞋可以去看下；
第一：检查是否装有SD卡；  
第二： 修改读入的地方(备注1)
```        
fis=this.openFileInput("save.android");//这里没有路径，路径是默认的data-data-com.android-files下替换成我们的SD卡的路径就可以了：
File path = new File("/sdcard/android/save.android");//这里新建一个File目录路径          
fis = new FileInputStream(path);
```
传入路径第三 : 
修改写入的地方(备注2)fos=this.openFileOutput("save.android",MODE_PRIVATE);这里也是默认路径,需要对其修改,注意：这里修改了，那么在finally中的判定大家也要对应的适当修改；注意：如果是系统路径，当没有此文件的时候，android会默认创建一个！但是我们放入SD卡的时候要自己创建目录路径和文件！
```    
if (Environment.getExternalStorageState() != null) {// 这个方法在试探终端是否有sdcard!  
    Log.v("android", "有SD卡");
    File path = new File("/sdcard/android");// 创建目录
    File f = new File("/sdcard/android/save.android");// 创建文件
    if (!path.exists()) {// 目录存在返回false  
        path.mkdirs();// 创建一个目录
    }  
    if (!f.exists()) {// 文件存在返回false  
        f.createNewFile();// 创建一个文件
    }  
    fos = new FileOutputStream(f);// 将数据存入sd卡中
} 
```
第四： 因为我们要在SD卡中进行写入的操作，所以要在配置文件中声明权限！这一句就是啦~    
```       
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/> 
```
为了让大家看到所放的位置，所以把整个xml放出来供参考；那么当创建路径和文件的时候，我们对其检查SD卡中是否已经存在exists()方法，如果已经存在就不去创建，这样避免下次再次写入数据的时候又新建了文件和路径、其实我们在可以在启动程序的时候判断如果没有此文件，我们可以直接紧接着创建一个文件，这些都属于优化上的了，我主要是让大家引入，学会，那么其他的简化啦，优化啦，其他方式去实现啦都留给各位同学自己了、OK、今天就先介绍到这里，后面会单独剖析SQLite如何存入数据，以及对数据操作的！