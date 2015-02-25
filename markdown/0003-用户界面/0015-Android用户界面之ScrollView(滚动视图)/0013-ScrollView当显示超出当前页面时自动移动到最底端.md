卷轴视图(ScrollView)是指当拥有很多内容，一屏显示不完时，需要通过滚动来显示视图。比如在做一个阅读器的时候，文章很长，一页显示不完，那么就需要使用卷轴视图来滚动显示下一页。 
```  
private ScrollView mScrollView;
private LinearLayout mLayout;
private final Handler mHandler = new Handler();
mScrollView = (ScrollView) findViewById(R.id.scroll);
mLayout = (LinearLayout) findViewById(R.id.linearlayout);// linearlayout外层为
mHandler.post(mScrollToBottom);
private Runnable mScrollToBottom = new Runnable() {
	@Override
	public void run() {
		int off = mLayout.getMeasuredHeight() - mScrollView.getHeight();
		if (off > 0) {
			mScrollView.scrollTo(0, off);
		}
	}
};
```
在Android，一个单独的TextView是无法滚动的，需要放在一个ScrollView中。ScrollView提供了一系列的函数，其中fullScroll用来实现home和end键的功能，也就是滚动到顶部和底部。 
但是，如果在TextView的append后面马上调用fullScroll，会发现无法滚动到真正的底部，这是因为Android下很多（如果不是全部的话）函数都是基于消息的，用消息队列来保证同步，所以函数调用多数是异步操作的。当TextView调用了append会，并不等text显示出来，而是把text的添加到消息队列之后立刻返回，fullScroll被调用的时候，text可能还没有显示，自然无法滚动到正确的位置。 
解决的方法其实也很简单，使用post： 
```  
final ScrollView svResult = (ScrollView) findViewById(R.id.svResult);
svResult.post(new Runnable() {
	public void run() {
		svResult.fullScroll(ScrollView.FOCUS_DOWN);
	}
});
```
Android将ScrollView移动到最底部 
scrollTo方法可以调整view的显示位置。 
在需要的地方调用以下方法即可。 
scroll表示外层的view，inner表示内层的view，其余内容都在inner里。 
注意，方法中开一个新线程是必要的。 
否则在数据更新导致换行时getMeasuredHeight方法并不是最新的高度。 
```  
public static void scrollToBottom(final View scroll, final View inner) {
	Handler mHandler = new Handler();
	mHandler.post(new Runnable() {
		public void run() {
			if (scroll == null || inner == null) {
				return;
			}
			int offset = inner.getMeasuredHeight() - scroll.getHeight();
			if (offset < 0) {
				offset = 0;
			}
			scroll.scrollTo(0, offset);
		}
	});
}
```