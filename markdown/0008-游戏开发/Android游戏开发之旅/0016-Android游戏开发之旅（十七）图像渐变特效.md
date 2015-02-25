在android游戏开发中我们不免要涉及到一些图形特效处理，今天主要看下Android平台下实现渐变效果。在android.graphics中我们可以找到有关Gradient字样的类，比如LinearGradient线性渐变、RadialGradient径向渐变和角度渐变SweepGradient三种，他们的基类为android.graphics.Shader。为了显示出效果android123使用一个简单的例子来说明。
#### 一、LinearGradient线性渐变
```  
LinearGradient(float x0, float y0, float x1, float y1, int[] colors, float[] positions, Shader.TileMode tile) 
LinearGradient(float x0, float y0, float x1, float y1, int color0, int color1, Shader.TileMode tile)
```
使用实例如下 
```  
p.setShader(lg);
canvas.drawCicle(0,0,200,p); //参数3为画圆的半径，类型为float型。
```
#### 二、 RadialGradient镜像渐变
有了上面的基础，我们一起来了解下径向渐变。和上面参数唯一不同的是，径向渐变第三个参数是半径，其他的和线性渐变相同。
```  
RadialGradient(float x, float y, float radius, int[] colors, float[] positions, Shader.TileMode tile) 
RadialGradient(float x, float y, float radius, int color0, int color1, Shader.TileMode tile)
```
#### 三、 SweepGradient角度渐变
对于一些3D立体效果的渐变可以尝试用角度渐变来完成一个圆锥形，相对来说比上面更简单，前两个参数为中心点，然后通过载入的颜色来平均的渐变渲染。 
```  
SweepGradient(float cx, float cy, int[] colors, float[] positions) //对于最后一个参数SDK上的描述为May be NULL. The relative position of each corresponding color in the colors array, beginning with 0 and ending with 1.0. If the values are not monotonic, the drawing may produce unexpected results. If positions is NULL, then the colors are automatically spaced evenly.，所以建议使用下面的重载方法，本方法一般为NULL即可。
SweepGradient(float cx, float cy, int color0, int color1)
```