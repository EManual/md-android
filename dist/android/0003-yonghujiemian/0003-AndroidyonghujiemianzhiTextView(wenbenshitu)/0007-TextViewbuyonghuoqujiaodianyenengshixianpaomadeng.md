之前在网上找了很多关于TextView的跑马灯效果实现的例子，实现起来都存在一些问题，例如一种是完全重画一个跑马灯，还有就是只设置TextView的相关属性使其具有跑马灯的效果，总的来说这两种方法都是可行的，但是都有其不足之处，第一种太复杂，实现起来比较麻烦，第二种呢，它只能在TextView获得焦点的时候才有跑马灯的效果，这样有时候并不能达到我们所要求的效果。我通过网上的一些例子自己在做了一些改动，就实现了现在不用获取焦点也能“跑”起来的效果。具体代码如下
首先，写一个类，让其继承自TextView：
```  
public class MarqueeText extends TextView {
	public MarqueeText(Context con) {
		super(con);
	}
	public MarqueeText(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public MarqueeText(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
	@Override
	public boolean isFocused() {
		return true;
	}
	@Override
	protected void onFocusChanged(boolean focused, int direction,
			Rect previouslyFocusedRect) {
	}
}
```
然后再将我们已经写好的这个控件（MarqueeText）放到布局文件中，例如main.xml:
```  
<TextView
    android:layout_width="400dip"
    android:layout_height="wrap_content"
    android:layout_marginBottom="25dip"
    android:layout_marginLeft="80dip"
    android:background="2FFFFFFF"
    android:ellipsize="marquee"
    android:focusable="true"
    android:focusableInTouchMode="true
    android:marqueeRepeatLimit="marquee_forever"
    android:scrollHorizontally="true"
    android:text="这才是真正的文字跑马灯效果,文字移动速度，文字移动方向，文字移动的样式，动画等等……
    android:textColor="@android:color/black"
    android:textSize="25sp" >
</TextView><!-- 在布局文件中用自己写的控件只需要写类的全名就行，如下com.example.这是包名，后面再跟类名就行了 -->
<com.example.MarqueeText
    android:id="@+id/AMTV1"
    android:layout_width="400dip"
    android:layout_height="wrap_content"
    android:layout_marginLeft="80dip"
    android:background="2FFFFFFF"
    android:ellipsize="marquee"
    android:focusable="true"
    android:focusableInTouchMode="true"
    android:lines="1
    android:marqueeRepeatLimit="marquee_forever"
    android:scrollHorizontally="true"
    android:text="这才是真正的文字跑马灯效果,文字移动速度，文字移动方向，文字移动的样式，动画等等……
    android:textColor="@android:color/black"
    android:textSize="25sp" />
```
前一个TextView是用Android自带的跑马灯效果，后一个就是咱自己的。至于Activity中怎么写这里就不多说了，没有什么特殊的设置。
关于MarqueeText类中为什么要复写onFocusChanged()方法，那是因为如果不写，在Textview 获得焦点后，再失去焦点时 字就会停止“跑”了，所以如果想让它一直跑下去就复写onFocusChanged(),并且里面什么也不做（主要是不能调用父类的方法）。
好了，一点小小心得，真是献丑了。