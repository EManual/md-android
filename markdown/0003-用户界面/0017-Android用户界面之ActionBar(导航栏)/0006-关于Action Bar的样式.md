因为适配的手机没有menu键，我想在activity中的title上面添加一个actionbar目前的代码是模仿的google的demo做出来之后显示的效果是在title的最右侧确实有actionbar但是样式是显示第一个选项的文本内容和下箭头，然后点进去之后是选项，包括显示在title的第一个选项，我想让他只显示那个下箭头，点击之后在现实具体的条目，在title上不显示第一条的内容，应该怎样设计呢下面的图片第一个是当前的效果，第二个图类似于期待的效果，但是每一条应该是垂直方向罗列的，求各位高人指点啊个人觉得应该actionbar的样式问题
![img](http://emanual.github.io/md-android/img/view_actionbar/view_actionbar.jpg)  
目前的
![img](http://emanual.github.io/md-android/img/view_actionbar/view_actionbar2.jpg)  
期望点击三个点的那个按钮，弹出下面四个选项
补充一下文档中的demo的代码部分是这样实现的
#### 提供下拉导航功能
i. 利用下拉选择项和布局，构建SpinnerAdapter。
```  
SpinnerAdapter mSpinnerAdapter = ArrayAdapter.createFromResource(this, R.array.action_list, android.R.layout.simple_spinner_dropdown_item);
```
ii.  实现ActionBar.OnNavigationListener，来记录用户选择list中项目的行为。
```  
public boolean onNavigationItemSelected(int position, long itemId)
```
iii. 设置导航模式为ActionBar.NAVIGATION_MODE_LIST
```  
ActionBar actionBar = getActionBar();
actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_LIST);
```
iv. 设置回调函数setListNavigationCallbacks()
```  
actionBar.setListNavigationCallbacks(mSpinnerAdapter, mNavigationCallback);
```