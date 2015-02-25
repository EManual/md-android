Activity之间简单的数据传递，可能经常用，今天遇到要传递一个ArrayList<MyClass>的问题。
花费了一点时间搞定，也学习了一些东西。
#### 1. 使用 Serializable 方法
将类的实例序列化然后再做存储或者传输在JAVA中较为常见，在Android也可用。
具体看代码吧，比较简单。
```  
public class MyClass implements Serializable {
	private static final long serialVersionUID = 1L;
	public String userName;
	public String psw;
	public int age;
}
```
一个自定义类，实现Serializable接口。
```  
findViewById(R.id.send_arraylist_button).setOnClickListener(new Button.OnClickListener() {
	@Override
	public void onClick(View v) {
		ArrayList<MyClass> arrayList = new ArrayList<MyClass>();
		for (int i = 0; i < 10; i++) {
			MyClass myClass = new MyClass();
			myClass.userName = "a" + i;
			myClass.psw = "b" + i;
			myClass.age = 10 + i;
			arrayList.add(myClass);
		}
		Intent intent = new Intent();
		intent.putExtra("key", arrayList);
		intent.setClass(MainActivity.this, ResultActivity.class);
		startActivity(intent);
	}
});
```
一个Activity中传递。
```  
ArrayList<MyClass> arrayList = (ArrayList<MyClass>) getIntent().getSerializableExtra("key");
String result = "" ;
for (MyClass myClass : arrayList) {
	result += myClass.userName + "--" + myClass.psw + "--" + myClass.age + "\n";
}
((TextView)findViewById(R.id.show_result_textview)).setText(result);
```
另一个Activity中接收
#### 2. 使用Parcelable 方法
Android内存受限，迫使其封装了Parcel容器来替代Serializable方法。
代码中做了一些注释，这里就不再解释了。
```  
import android.os.Parcel;
import android.os.Parcelable;
[Tags]/**
 [Tags]* Parcel类:http://developer.android.com/reference/android/os/Parcel.html <br>
 [Tags]* 封装数据的容器，封装后的数据可以通过Intent或IPC传递 <br>
 [Tags]* 
 [Tags]* Parcelable接口：http://developer.android.com/reference/android/os/Parcelable.
 [Tags]* html <br>
 [Tags]* 自定义类继承该接口后，其实例化后能够被写入Parcel或从Parcel中恢复。 <br>
 [Tags]* 
 [Tags]* 如果某个类实现了这个接口，那么它的对象实例可以写入到 Parcel 中，并且能够从中恢复， 并且这个类必须要有一个 static 的 field
 [Tags]* ，并且名称要为 CREATOR ，这个 field 是某个实现了 Parcelable.Creator 接口的类的对象实例。
 [Tags]*/
public class MyClass2 implements Parcelable {
	public String userName;
	public String psw;
	public int age;
	// 静态的Parcelable.Creator接口
	public static final Parcelable.Creator<MyClass2> CREATOR = new Creator<MyClass2>() {
		// 创建出类的实例，并从Parcel中获取数据进行实例化
		public MyClass2 createFromParcel(Parcel source) {
			MyClass2 myClass2 = new MyClass2();
			myClass2.userName = source.readString();
			myClass2.psw = source.readString();
			myClass2.age = source.readInt();

			return myClass2;
		}
		public MyClass2[] newArray(int size) {
			return new MyClass2[size];
		}
	};
	@Override
	public int describeContents() {
		return 0;
	}
	// 将数据写入外部提供的Parcel中
	@Override
	public void writeToParcel(Parcel dest, int flags) {
		dest.writeString(userName);
		dest.writeString(psw);
		dest.writeInt(age);
	}
}
```
一个自定义类，注释中又说明，看代码。
```  
//use Parcelable
findViewById(R.id.send_arraylist_button).setOnClickListener(new Button.OnClickListener() {
	@Override
	public void onClick(View v) {
		ArrayList<MyClass2> arrayList = new ArrayList<MyClass2>();
		for (int i = 0; i < 10; i++) {
			MyClass2 myClass2 = new MyClass2();
			myClass2.userName = "a" + i;
			myClass2.psw = "b" + i;
			myClass2.age = 10 + i;
			arrayList.add(myClass2);
		}
		Intent intent = new Intent();
		intent.putParcelableArrayListExtra("key", arrayList);
		intent.setClass(MainActivity.this, ResultActivity.class);
		startActivity(intent);
	}
});
```
发送
```  
//use Parcelable
ArrayList<MyClass2> arrayList = (ArrayList<MyClass2>) getIntent().getSerializableExtra("key");
String result = "" ;
for (MyClass2 myClass2 : arrayList) {
	result += myClass2.userName + "--" + myClass2.psw + "--" + myClass2.age + "\n";
}
((TextView)findViewById(R.id.show_result_textview)).setText(result);
```
接收