前言
由于各个Android平板触摸屏的材质不一样，滑动效果会有一些区别，有的比较灵敏，有的比较迟钝，这里就遇到了要求控制滑动速度的需求。
正文
翻阅查找ScrollView的文档并搜索了一下没有发现直接设置的属性和方法，这里通过继承来达到这一目的。
```  
 /**
  * 快/慢滑动ScrollView
  */
public class SlowScrollView extends ScrollView {
	public SlowScrollView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
	public SlowScrollView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public SlowScrollView(Context context) {
		super(context);
	}
	 /**
	  * 滑动事件
	  */
	@Override
	public void fling(int velocityY) {
		super.fling(velocityY / 4);
	}
}
```
代码说明：
重点在"velocityY / 4"，这里意思是滑动速度减慢到原来四分之一的速度，这里大家可以根据自己的需求加快或减慢滑动速度。