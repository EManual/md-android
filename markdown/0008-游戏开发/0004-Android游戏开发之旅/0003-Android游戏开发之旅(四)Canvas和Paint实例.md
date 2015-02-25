在android游戏开发之旅（三）View类详解中提到了onDraw方法，有关详细的实现我们今天主要说下Android的Canvas和Paint对象的使用实例。
Canvas类主要实现了屏幕的绘制过程，其中包含了很多实用的方法，比如绘制一条路径、区域、贴图、画点、画线、渲染文本，下面是Canvas类常用的方法，当然Android开发网提示大家很多方法有不同的重载版本，参数更灵活。
```  
void drawRect(RectF rect, Paint paint) //绘制区域，参数一为RectF一个区域
void drawPath(Path path, Paint paint) //绘制一个路径，参数一为Path路径对象 
void  drawBitmap(Bitmap bitmap, Rect src, Rect dst, Paint paint)  //贴图，参数一就是我们常规的Bitmap对象，参数二是源区域(Android123提示这里是bitmap)，参数三是目标区域(应该在canvas的位置和大小)，参数四是Paint画刷对象，因为用到了缩放和拉伸的可能，当原始Rect不等于目标Rect时性能将会有大幅损失。 
void  drawLine(float startX, float startY, float stopX, float stopY, Paint paint)  //画线，参数一起始点的x轴位置，参数二起始点的y轴位置，参数三终点的x轴水平位置，参数四y轴垂直位置，最后一个参数为Paint画刷对象。
void  drawPoint(float x, float y, Paint paint) //画点，参数一水平x轴，参数二垂直y轴，第三个参数为Paint对象。
void drawText(String text, float x, float y, Paint paint)  //渲染文本，Canvas类除了上面的还可以描绘文字，参数一是String类型的文本，参数二x轴，参数三y轴，参数四是Paint对象。
void  drawTextOnPath(String text, Path path, float hOffset, float vOffset, Paint paint) //在路径上绘制文本，相对于上面第二个参数是Path路径对象
```
从上面来看我们可以看出Canvas绘制类比较简单同时很灵活，实现一般的方法通常没有问题，同时可以叠加的处理设计出一些效果，不过细心的网友可能发现最后一个参数均为Paint对象。如果我们把Canvas当做绘画师来看，那么Paint就是我们绘画的工具，比如画笔、画刷、颜料等等。
Paint类常用方法:
```  
void setARGB(int a, int r, int g, int b)  设置Paint对象颜色，参数一为alpha透明通道
void setAlpha(int a)  设置alpha不透明度，范围为0~255
void setAntiAlias(boolean aa)  //是否抗锯齿
void setColor(int color)  //设置颜色，这里Android内部定义的有Color类包含了一些常见颜色定义
void setFakeBoldText(boolean fakeBoldText)  //设置伪粗体文本
void setLinearText(boolean linearText)  //设置线性文本
PathEffect setPathEffect(PathEffect effect)  //设置路径效果
Rasterizer setRasterizer(Rasterizer rasterizer) //设置光栅化
Shader setShader(Shader shader)  //设置阴影 
void setTextAlign(Paint.Align align)  //设置文本对齐
void setTextScaleX(float scaleX)  //设置文本缩放倍数，1.0f为原始
void setTextSize(float textSize)  //设置字体大小
Typeface setTypeface(Typeface typeface)  //设置字体，Typeface包含了字体的类型，粗细，还有倾斜、颜色等。
void setUnderlineText(boolean underlineText)  //设置下划线
```
最终 Canvas和Paint在onDraw中直接使用
```  
@Override
protected void onDraw(Canvas canvas) {
	Paint paintRed=new Paint();
	paintRed.setColor(Color.Red);
	canvas.drawPoint(11,3,paintRed); //在坐标11,3上画一个红点
}
```