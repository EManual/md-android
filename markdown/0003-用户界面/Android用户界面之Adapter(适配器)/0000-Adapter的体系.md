在Android开发中列表的使用是十分常见的。google对列表的封装使列表既有显示传统文本列表的能力，也有加入了诸如选择项、复选项等处理事件的能力。这里写一些我这几天对这个问题的理解。
在android的api中，LIST和adapter都被放在了android.widget包内。包内的具体结构我这里先不展示了，主要侧重列表和adapter。adapter的作用就是将要在列表内显示的数据和列表本身结合起来。列表本身只完成显示的作用，其实他就是继承自VIEWGROUP类。但是他又有一个独特的函数就是setAdapter()就是完成了view和adapter的结合。adapter如同其本身含义，其实就是一个适配器，他可以对要显示的数据进行统一的封装，主要是将数据变成view提供给list。
我们先来看看adapter的体系：
```  
public interface Adapter----0层（表示继承体系中的层次）
public interface ExpandableListAdapter---(无所谓层次因为没有其他接口继承实现它)
```
这是adapter的始祖，其他个性化的adapter均实现它并加入自己的接口。
```  
public interface ListAdapter----1层
public interface SpinnerAdapter----1层
public interface WrapperListAdapter----2层（实现ListAdapter）
```
以上接口层面上的体系已经完了。可以看出来作为widgetview的桥梁adapter其实只分为2种：ListAdapter和SpinnerAdapter以及ExpandableListAdapter。也就是说所有widget也就是基于list和spinne与ExpandableList三种view形式的。
由于在实际使用时，我们需要将数据加入到Adapter，而以接口形式呈现的adapter无法保存数据，于是Adapter就转型为类的模式。
public abstract class BaseAdapter----2层（实现了ListAdapter和SpinnerAdapter）
以抽象类的形式出现构造了类型态下的顶层抽象，包容了List和Spinner
```  
public class ArrayAdapter----3层
public class SimpleAdapter---3层
public class CursorAdapter----3层（CursorAdapter其后还有子类这里先不探讨）
```
基本体系有了之后，让我们看看顶层Adapter里有哪些方法(只列举常用的)：
```  
abstract Object getItem(int position)
abstract int getCount()
abstract long getItemId(int position)
abstract int getItemViewType(int position)
abstract View getView(int position,View convertVeiw,ViewGroup parent)
```
以上是比较重要的方法，ArrayAdapter他们也是重新实现以上方法的。在实际的开发过程中，往往我们要自己做属于自己的Adapter，以上方法都是需要重新实现的。这个在android提供的APIdemo例子中可以看到。