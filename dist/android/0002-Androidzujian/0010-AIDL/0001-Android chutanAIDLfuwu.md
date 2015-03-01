使用bindService方法来绑定AIDL服务。其中需要使用Intent对象指定AIDL服务的ID，也就是<action>标签中android:name属性的值。
在绑定时需要一个ServiceConnection对象。创建ServiceConnection对象的过程中如果绑定成功，系统会调用onServiceConnected方法，通过该方法的service参数值可获得AIDL服务对象。
首先运行AIDL服务程序，然后运行客户端程序，单击【绑定AIDL服务】按钮，如果绑定成功，【调用AIDL服务】按钮会变为可选状态，单击这个按钮，会输出getValue方法的返回值，如图所示。

![img](http://emanual.github.io/md-android/img/component_aidl/02_aidl.jpg)  

AIDL服务只支持有限的数据类型，因此，如果用AIDL服务传递一些复杂的数据就需要做更一步处理。AIDL服务支持的数据类型如下：
Java的简单类型（int、char、boolean等）。不需要导入（import）。
String和CharSequence。不需要导入（import）。
List和Map。但要注意，List和Map对象的元素类型必须是AIDL服务支持的数据类型。不需要导入（import）。
AIDL自动生成的接口。需要导入（import）。
实现android.os.Parcelable接口的类。需要导入（import）。
其中后两种数据类型需要使用import进行导入，将在本章的后面详细介绍。
传递不需要import的数据类型的值的方式相同。传递一个需要import的数据类型的值（例如，实现android.os.Parcelable接口的类）的步骤略显复杂。除了要建立一个实现android.os.Parcelable接口的类外，还需要为这个类单独建立一个aidl文件，并使用parcelable关键字进行定义。具体的实现步骤如下：
（1）建立一个IMyService.aidl文件，并输入如下代码：
```  
interface IMyService { 
	Map getMap(String country, Product product);
	Product getProduct();
}
```
在编写上面代码时要注意如下两点：
Product是一个实现android.os.Parcelable接口的类，需要使用import导入这个类。
如果方法的类型是非简单类型，例如，String、List或自定义的类，需要使用in、out或inout修饰。其中in表示这个值被客户端设置；out表示这个值被服务端设置；inout表示这个值既被客户端设置，又被服务端设置。
（2）编写Product类。该类是用于传递的数据类型，代码如下：
```  
import android.os.Parcel;
import android.os.Parcelable;
public class Product implements Parcelable {
	private int id;
	private String name;
	private float price;
	public static final Parcelable.Creator<Product> CREATOR = new Parcelable.Creator<Product>() {
		public Product createFromParcel(Parcel in) {
			return new Product(in);
		}
		public Product[] newArray(int size) {
			return new Product[size];
		}
	};
	public Product() {
	}
	private Product(Parcel in) {
		readFromParcel(in);
	}
	@Override
	public int describeContents() {
		return 0;
	}
	public void readFromParcel(Parcel in) {
		id = in.readInt();
		name = in.readString();
		price = in.readFloat();
	}
	@Override
	public void writeToParcel(Parcel dest, int flags) {
		dest.writeInt(id);
		dest.writeString(name);
		dest.writeFloat(price);
	}
	// 此处省略了属性的getter和setter方法 ... ...
}
```
在编写Product类时应注意如下3点：
Product类必须实现android.os.Parcelable接口。该接口用于序列化对象。在Android中之所以使用Pacelable接口序列化，而不是java.io.Serializable接口，是因为Google在开发Android时发现Serializable序列化的效率并不高，因此，特意提供了一个Parcelable接口来序列化对象。
在Product类中必须有一个静态常量，常量名必须是CREATOR，而且CREATOR常量的数据类型必须是Parcelable.Creator。