谈谈android大众常用的五种布局。 
答：在Android中，共有五种布局方式，分别是：FrameLayout(框架布局)，LinearLayout(线性布局)，AbsoluteLayout(绝对布局)，RelativeLayout(相对布局)，TableLayout(表格布局)。 
#### （1）FrameLayout
框架布局,放入其中的所有元素都被放置在最左上的区域，而且无法为这些元素指定一个确切的位置,下一个子元素会重叠覆盖上一个子元素，适合浏览单张图片。 
#### （2）LinearLayout 
线性布局,是应用程序中最常用的布局方式，主要提供控件水平或者垂直排列的模型，每个子组件都是以垂直或水平的方式来定位.(默认是垂直) 
#### （3）AbsoluteLayout 
绝对定位布局,采用坐标轴的方式定位组件，左上角是（0，0）点，往右x轴递增，往下Y轴递增,组件定位属性为android:layout_x和android:layout_y来确定坐标。 
#### （4）RelativeLayout 
相对布局,根据另外一个组件或是顶层父组件来确定下一个组件的位置。和CSS里面的类似。 
#### （5）TableLayout 
表格布局,类似Html里的Table.使用TableRow来布局，其中TableRow代表一行，TableRow的每一个视图组件代表一个单元格。 

