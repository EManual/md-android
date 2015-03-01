今天我们继续处理上次Android游戏开发之旅（四）Canvas和Paint实例中提到的Path路径和Typeface字体两个类。对于Android游戏开发或者说2D绘图中来讲Path路径可以用强大这个词来形容。在Photoshop中我们可能还记得使用钢笔工具绘制路径的方法。Path路径类在位于android.graphics.Path中，Path的构造方法比较简单，如下
```  
Path cwj = new Path();  //构造方法
```
下面我们画一个封闭的原型路径，我们使用Path类的addCircle方法
```  
cwj.addCircle(10,10,50,Direction.CW); //参数一为x轴水平位置，参数二为y轴垂直位置，第三个参数为圆形的半径，最后是绘制的方向，CW为顺时针方向，而CCW是逆时针方向
```
结合Android上次提到的Canvas类中的绘制方法drawPath和drawTextOnPath，我们继续可以在onDraw中加入。
```  
canvas.drawPath(cwj,paintPath); //Android123提示大家这里paintPath为路径的画刷颜色，可以见下文完整的源代码。
canvas.drawTextOnPath("Android123 - CWJ",cwj,0,15,paintText); //将文字绘制到路径中去，
```
有关drawTextOnPath的参数如下:
方法原型
```  
public void drawTextOnPath (String text, Path path, float hOffset, float vOffset, Paint paint)
```
参数列表
text  为需要在路径上绘制的文字内容。
path 将文字绘制到哪个路径。 
hOffset  距离路径开始的距离
vOffset  离路径的上下高度，这里Android开发网提示大家，该参数类型为float浮点型，除了精度为8位小数外，可以为正或负，当为正时文字在路径的圈里面，为负时在路径的圈外面。
paint 最后仍然是一个Paint对象用于制定Text本文的颜色、字体、大小等属性。
下面是我们的onDraw方法中如何绘制路径的演示代码为:
```  
@Override
protected void onDraw(Canvas canvas) {
	Paint paintPath = new Paint();
	Paint paintText = new Paint();
	paintPath.setColor(Color.Red); //路径的画刷为红色
	paintText.setColor(Color.Blue); //路径上的文字为蓝色
	Path pathCWJ = new Path();
	pathCWJ.addCircle(10,10,50,Direction.CW); // 半径为50px，绘制的方向CW为顺时针
	canvas.drawPath(pathCWJ,paintPath);
	canvas.drawTextOnPath("Android123 - CWJ",pathCWJ,0,15,paintText); //在路径上绘制文字
}
```
有关路径类常用的方法如下:
```  
void addArc(RectF oval, float startAngle, float sweepAngle)  //为路径添加一个多边形
void addCircle(float x, float y, float radius, Path.Direction dir)  //给path添加圆圈
void addOval(RectF oval, Path.Direction dir)  //添加椭圆形
void addRect(RectF rect, Path.Direction dir)  //添加一个区域
void addRoundRect(RectF rect, float[] radii, Path.Direction dir)  //添加一个圆角区域
boolean isEmpty()  //判断路径是否为空
void transform(Matrix matrix)  //应用矩阵变换
void transform(Matrix matrix, Path dst)  //应用矩阵变换并将结果放到新的路径中，即第二个参数。
```
有关路径的高级效果大家可以使用PathEffect类，有关路径的更多实例Android123将在今后的游戏开发实战中讲解道。
Typeface字体类
平时我们在TextView中需要设置显示的字体可以通过TextView中的setTypeface方法来指定一个Typeface对象，因为Android的字体类比较简单，我们列出所有成员方法
```  
static Typeface  create(Typeface family, int style)  //静态方法，参数一为字体类型这里是Typeface的静态定义，如宋体，参数二风格，如粗体，斜体
static Typeface  create(String familyName, int style)  //静态方法，参数一为字体名的字符串，参数二为风格同上，这里我们推荐使用上面的方法。
static Typeface  createFromAsset(AssetManager mgr, String path)  //静态方法，参数一为AssetManager对象，主要用于从APK的assets文件夹中取出字体，参数二为相对于Android工程下的 assets文件夹中的外挂字体文件的路径。
static Typeface  createFromFile(File path)  //静态方法，从文件系统构造一个字体，这里参数可以是sdcard中的某个字体文件
static Typeface  createFromFile(String path)  //静态方法，从指定路径中构造字体
static Typeface  defaultFromStyle(int style) //静态方法，返回默认的字体风格
int  getStyle()  //获取当前字体风格
final boolean  isBold()  //判断当前是否为粗体
final boolean  isItalic()  //判断当前风格是否为斜体
```
本类的常量静态定义，首先为字体类型名称
```  
Typeface DEFAULT 
Typeface DEFAULT_BOLD
Typeface MONOSPACE
Typeface SANS_SERIF
Typeface SERIF
```
字体风格名称
```  
int BOLD 
int BOLD_ITALIC 
int ITALIC
int NORMAL
```