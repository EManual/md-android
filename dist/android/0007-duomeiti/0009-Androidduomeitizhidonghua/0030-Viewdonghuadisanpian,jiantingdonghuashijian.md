通过这些知识大家应该能够做出常用的动画效果了，但是我们在制作动画效果时，往往都不仅仅只是显示出动画效果而已，更普遍的情况是，在动画完成，或者动画刚开始时，我们需要执行一系列操作，比如操作数据库，网络通信等。而android也为我们提供了相应的接口，那就是AnimationListener，下面我们来看一段代码：
```  
myAnim.setAnimationListener(new AnimationListener() {
	@Override
	public void onAnimationStart(Animation animation) {
		// 动画开始时让View可见
		list.setVisibility(View.VISIBLE);
	}
	// 当动画重复播放时的事件
	@Override
	public void onAnimationRepeat(Animation animation) {
	}
	@Override
	public void onAnimationEnd(Animation animation) {
		// 动画结束时让View隐藏
		list.setVisibility(View.GONE);
	}
});
```
上面的代码中，我们实现了我们自己的AnimationListener接口，在动画开始和结束的时候，显示和隐藏我们的View视图。有了这个接口，会为我们开发动画效果增加了不少的灵活性，相信这个对大家会很有帮助～