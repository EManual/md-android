上面的内容的功能看起来非常明显。这个特殊文件定义了一个相关的布局，这就意味着通过一个元素到另一个元素的关系或是它们父元素的关系来描述。对于视图来说，有一些用于布局的方法，但是在本文中只关注于上述的xml文件。
RealtiveLayout中包含了一个填充整个屏幕的文本框（也就是我们的LocateMe activity）。这个LocateMe activity在默认情况下是全屏的，因此，文本框将继承这个属性，并且文本框将在屏幕的左上角显示。另外，必须为这个XML文件设置一个引用数，以便Android可以在源代码中找到它。在默认情况下，这些引用数被保存在R.Java中，代码如下：
```  
public final class R {
	public static final class layout {
		public static final int main = 0x7f030001;
	}
}
```
Android实例、这个演示应用程序将演示了用户的当前的经度和纬度。onCreate构造方法将和上面的例子基本相同，除了在其中加入了键盘处理，现在让我们看一下onKeyDown的代码。
```  
public boolean onKeyDown(int keyCode, KeyEvent event) {
	if (keyCode != KeyEvent.KEYCODE_DPAD_CENTER || m_bLoading) {
		return true;
	}
	m_bLoading = true;
	getLocation();
	return true;
}
```
下面让我们来解释一下这段代码，首先，这段代码检查了当前被按下的键，但还没有开始处理。而是在getLocation方法中处理这一切的。然后，将装载flag标志以及调用getLocation方法，下面是getLocation方法的代码。
```  
private void getLocation() {
	Location loc;
	LocationManager locMan;
	LocationProvider locPro;
	List<LocationProvider> proList;
	setContentView(R.layout.laoding);
	locMan = (LocationManager) getSystemService(LOCATION_SERVICE);
	proList = locMan.getProviders();
	locPro = proList.get(0);
	loc = locMan.getCurrentLocation(locPro.getName());
	Lat = (float) loc.getLatitude();
	Lon = (float) loc.getLongitude();
	CreateView();
	setContentView(customView);
}
```
目前我们可以使用位置管理器和位置提供者进行getCurrentLocation的调用。这个方法返回本机的当前位置的一个快照，这个快照将以Location对象形式提供。在手持设备中，我们可以获得当前位置的经度和纬度。现在，使用这个虚拟的手持设备，我们可以获得这个例子程序的最终结果： 建立了显示一个定制的视图。
使用定制视图在最简单的窗体中，一个Android中的视图仅仅需要重载一个onDraw方法。定制视图可以是复杂的3D实现或是非常简单的文本形式。下面的CreateView方法列出了上面看到的内容。
```  
public void CreateView() {
	customView = new CustomView(this);
}
```
这个方法简单地调用了CustomView对象的构造方法。CustomView类的定义如下：
```  
public class CustomView extends View {
	LocateMe overlord;
	public CustomView(LocateMe pCtx) {
		super(pCtx);
		overlord = pCtx;
	}
	public void onDraw(Canvas cvs) {
		Paint p = new Paint();
		String sLat = "Latitude: " + overlord.getLat();
		String sLon = "Longitude: " + overlord.getLon();
		cvs.drawText(sLat, 32, 32, p);
		cvs.drawText(sLon, 32, 44, p);
	}
}
```
这个定制的Android视图获得了经度和违度的测试数据，并将这些数据显示在屏幕上。这要求一个指向LocateMe的指针，Activity类是整个应用程序的核心。它的两个方法是构造方法和onDraw方法。这个构造方法调用了超类的构造方法以及引起了Activity指针的中断。onDraw方法将建立一个新的Paint对象（这个对象封装了颜色、透明度以及其他的主题信息），这个对象将会访问颜色主题。在本程序中，安装了用于显示的字符串，并使用画布指针将它们画到屏幕上。这个和我们了解的J2ME游戏的画布看起来非常类似。