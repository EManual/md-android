Android布局是应用界面开发的重要一环，在Android中，共有五种布局方式，分别是：FrameLayout(框架布局)，LinearLayout(线性布局)，AbsoluteLayout(绝对布局)，RelativeLayout(相对布局)，TableLayout(表格布局)。
#### 一、FrameLayout
1、最简单的布局对象。
2、在屏幕上故意保留的空白空间，你可以之后填充一个单独的对象。
3、例如：一个你要更换的图片。
4、所有子元素都钉到屏幕的左上角。
5、不能为子元素指定位置。
这个布局可以看成是墙脚堆东西，有一个四方的矩形的左上角墙脚，我们放了第一个东西，要再放一个，那就在放在原来放的位置的上面，这样依次的放，会盖住原来的东西。这个布局比较简单，也只能放一点比较简单的东西。
#### 二、LinearLayout
1、在一个方向上(垂直或水平)对齐所有子元。
所有子元素一个跟一个地堆放。
2、一个垂直列表每行将只有一个子元素(无论它们有多宽)。
3、一个水平列表只是一列的高度（最高子元素的高度来填充）。
线性布局，这个东西，从外框上可以理解为一个div，他首先是一个一个从上往下罗列在屏幕上。每一个LinearLayout里面又可分为垂直布局（android:orientation="vertical"）和水平布局（android:orientation="horizontal"）。当垂直布局时，每一行就只有一个元素，多个元素依次垂直往下；水平布局时，只有一行，每一个元素依次向右排列。
linearLayout中有一个重要的属性 android:layout_weight="1"，这个weight在垂直布局时，代表行距；水平的时候代表列宽；weight值越大就越大。
#### 三、AbsoluteLayout
使子元素能够指明确切的X / Y坐标显示在屏幕上
1、(0,0)是左上角。
2、当你下移或右移时，坐标值增加。
允许元素重叠(但是不推荐)
注意:
1、一般建议不使用AbsoluteLayout除非你有很好的理由来使用它。
2、因为它相当严格并且在不同的设备显示中不能很好地工作。
绝对布局犹如div指定了absolute属性，用X,Y坐标来指定元素的位置android:layout_x="20px" android:layout_y="12px" 这种布局方式也比较简单，但是在垂直随便切换时，往往会出问题，而且多个元素的时候，计算比较麻烦。 
#### 四、RelativeLayout
让子元素指定它们相对于其他元素的位置(通过ID来指定)或相对于父布局对象 
相对布局可以理解为某一个元素为参照物，来定位的布局方式。主要属性有：
相对于某一个元素
```  
android:layout_below="@id/aaa" //该元素在 id为aaa的下面
android:layout_toLeftOf="@id/bbb" //改元素的左边是bbb
```
相对于父元素的地方
```  
android:layout_alignParentLeft="true" //在父元素左对齐
android:layout_alignParentRight="true" //在父元素右对齐
```
#### 五、TableLayout
把子元素放入到行与列中
不显示行、列或是单元格边界线
单元格不能横跨行，如HTML中一样
表格布局类似Html里面的Table。每一个TableLayout里面有表格行TableRow，TableRow里面可以具体定义每一个元素，设定他的对齐方式android:gravity="" 
每一个布局都有自己适合的方式，另外，这五个布局元素可以相互嵌套应用，做出美观的界面。