#### Gallery3D概述
Gallery3D的界面生成和普通的应用程序不一样。普通程序一般一个界面就是一个activity,布局用xml或代码都可以实现，界面切换是activity的切换方式；而Gallery3D没有用android的UI系统，而是用opengl画出来的,即界面是在同一个activity的，如主界面，缩略图界面，单张图片查看界面，标记界面等都属于同一个activity。
#### 主要线程介绍
在应用程序中有三个非常重要的线程存在：主线程(Gallery随activity的生命周期启动销毁)、MediaFeed初始化线程(进入程序时只运行一次，用于加载相册初始信息)、MediaFeed监听线程(一直在跑，监听相册和相片的变更)，其中MediaFeed初始化线程的工作是：调用MediaFeed 的loadMediaSets加载相册，MediaFeed监听线程MediaFeed.run()的工作是：根据“内容变化监听器“返回的媒体变动消息 (增删改)，持续不断的更新 MediaFeed中的相册和相片变量。
#### 控件
Gallery3D中定义了很多控件它们都继承自com.cooliris.media.Layer，分别代表不同场景和界面下的UI元素，具体有如下控件。
```  
com.cooliris.media.GridLayer:网格所略图显示和单个图片显示。
com.cooliris.media.BackgroundLayer:背景。
com.cooliris.media.HudLayer:相册显示。
com.cooliris.media.ImageButton:图片按钮(主要指进入Gallery后右上角的那个控件)。
com.cooliris.media.TimeBar:进入Gallery后下方可拖动的悬浮控件。
com.cooliris.media.MenuBar:点击图片时弹出的菜单按钮。
com.cooliris.media.PopupMenu:点击菜单按钮后弹出来的菜单项。
com.cooliris.media.PathBarLayer:如今Gallery后左上方显示图片路径的空间。
```
#### 渲染流程
Gallery3D的渲染从 RenderView 开始。RenderView 从 GLSurfaceView 继承而来，采用了通知型绘制模式，即通过调用requestRender 通知 RenderView 重绘屏幕。RenderView 将所有需要绘制的对象都保存一个 Lists中，Lists 包含了5个ArrayList，其定义如下所示：
```  
public final ArrayList<Layer> updateList = newArrayList<Layer>(); 
public final ArrayList<Layer> opaqueList = newArrayList<Layer>(); 
public final ArrayList<Layer> blendedList = newArrayList<Layer>(); 
public final ArrayList<Layer> hitTestList = newArrayList<Layer>(); 
public final ArrayList<Layer> systemList = new ArrayList<Layer>();
```
RenderView的onDrawFrame接口完成每一帧的绘制操作，绘制时遍历lists里每个list的每一个成员并调用其renderXXX函数。主要代码如下所示：
```  
final Lists lists = sLists;
final ArrayList<Layer> updateList = lists.updateList;
boolean isDirty = false;
for (int i = 0, size = updateList.size(); i != size; ++i) {
	boolean retVal = updateList.get(i).update(this, mFrameInterval);
	isDirty |= retVal;
}
if (isDirty) {
	requestRender();
}
// Clear the depth buffer.
gl.glClear(GL11.GL_DEPTH_BUFFER_BIT);
gl.glEnable(GL11.GL_SCISSOR_TEST);
gl.glScissor(0, 0, getWidth(), getHeight());
// Run the opaque pass.
gl.glDisable(GL11.GL_BLEND);
final ArrayList<Layer> opaqueList = lists.opaqueList;
for (int i = opaqueList.size() - 1; i >= 0; --i) {
	final Layer layer = opaqueList.get(i);
	if (!layer.mHidden) {
		layer.renderOpaque(this, gl);
	}
}
// Run the blended pass.
gl.glEnable(GL11.GL_BLEND);
final ArrayList<Layer> blendedList = lists.blendedList;
for (int i = 0, size = blendedList.size(); i != size; ++i) {
	final Layer layer = blendedList.get(i);
	if (!layer.mHidden) {
		layer.renderBlended(this, gl);
	}
}
gl.glDisable(GL11.GL_BLEND);
```