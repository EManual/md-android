本次讨论，我们主要涉及了AndroidManifest文件，以及少数xml文件。
AndroidManifest.xml是每个android程序中必须的文件。它位于application的根目录，描述了package中的全局数据，包括了package中暴露的组件（activities, services, 等等），它们各自的实现类，各种能被处理的数据和启动位置。
此文件一个重要的地方就是它所包含的intent-filters。这些filters描述了activity启动的位置和时间。
其中，我们着重讲了intent-filters，action为MAIN，则为程序的主入口，category为LAUNCHER则是在启动器中有图标。同时，我们也试验了，去掉命名空间语句是会出错的。
重点是我们在讨论过程中提出的几个问题，下面我整理了部分问题的答案：
#### 1.category的作用：
官方意思：Category是对目标组件类别信息的描述。这个可能很抽象，我的理解是，action定义了做什么，category就是定义了我们怎么做，比如我们洗衣服，假如这是一个activity的话，我们要洗涤，脱水等等操作就是action，来达到我们得目的，而用洗衣机洗，用手洗，这就是category，而我们具体的衣服，就是data了。
#### 2.@和？的区别和具体用法
@表示引用资源
@android:string表明引用的系统的(android.*)资源
@string表示引用应用内部资源
对于id,可以用@+id表明创建一个id
@表明了我们应用的资源是前边定义过的(或者在前一个项目中或者在Android 框架中)。
？表示引用主题属性，当你使用这个标记，你所提供的资源名必须能够在主题属性中找到，因为资源工具认为这个资源属性是被期望得到的，你不需要明确的指出他的类型
#### 3.什么时候要加上android.intent.category.DEFAULT？
我们几乎发现，隐式的Intent调用时都要加上android.intent.category.DEFAULT这个category，这是为什么呢？
因为每一个通过 startActivity() 方法发出的隐式 Intent 都至少有一个 category，就是 "android.intent.category.DEFAULT"，所以只要是想接收一个隐式 Intent 的 Activity 都应该包括 "android.intent.category.DEFAULT" category，不然将导致 Intent 匹配失败。
从上文中，我们可以得到两条额外信息：
```  
1，一个 Intent 可以有多个 category，但至少会有一个，也是默认的一个 category。
2，只有 Intent 的所有 category 都匹配上，Activity 才会接收这个 Intent。
```
#### 4.为什么把category那行去掉后不能启动程序？
根据上题，我们得知，必须默认一个DEFAULT属性的category，否则隐式调用此Intent是不能启动成功的，因此，去掉此行，程序不会报错，但是程序无法启动，因为无法通过Intent跳转到这个程序
#### 5.R.java下的attr的作用
R.java文件中默认有attr、drawable、layout、string等四个静态内部类，每个静态内部类分别对应着一种资源，如layout静态内部类对应layout中的界面文件，其中每个静态内部类中的静态常量分别定义一条资源标识符，如public staticfinal int main=0x7f030000;对应的是layout目录下的main.xml文件。
PS：添加资源的命名规则：资源文件只能以小写字母和下划线做首字母，随后的名字中只能出现 [a-z0-9_.] 这些字符，否则R.java文件不会自动更新，并且eclipse会提示错误
#### 6.Task机制
Android通过保持所有的activity在同一个任务中来保持用户体验。简单的的说，任务就是用户所体验到的“应用程序”。它是一组相关的activity，分配到一个栈中。栈中的根activity，是任务的开始——一般来说，它是用户组应用程序加载器中选择的activity。在栈顶的activity正是当前正在运行的——集中处理用户动作的那个。当一个activity启动了另外一个，这个新的activity将压入栈中，它将成为正在运行中的 activity。前一个activity保留在栈中。当用户按下后退按键，当前的这个activity将中栈中弹出，而前面的那个activity恢复成运行中状态。
#### 7.Intent Filter里设置优先级无作用，系统仍然会弹出activity的选择列表
经过试验，Intent Filter里设置优先级是有一定规律的，我们知道，用android：priority设置优先级的范围是-1000到1000，当我们设置两个Intent Filter的范围均为正整数或0的时候，优先级的设置没有发挥作用，只有当我们设置其中一个为负数的时候，优先级发挥了作用。以下是我所作的Intent Filter的测试列表：
```  
Intent Filter A的优先级	Intent Filter B的优先级	优先级发挥作用与否
1000	100		否
1000	-1000	是
1		-1		是
1		0		否
1000	0		否
-1		-1000	是
-1		-2		是
```
从上表可知，只要一个设置为负，则优先级的设置起作用。
以上是对activity的Intent Filter的设置，那么在对receiver的Intent Filter的设置时，我们即使设置了优先级，我们也只能对有序广播有效，对无序广播是无效的。
个人猜想：当我们activity的Intent Filter优先级均设置为正时，可能就与线程一样，优先级的设置只是系统考虑的一个方面，但不是绝对，因此系统仍然会把所有为正的Intent Filter过滤出来让用户选择，而当一个设置为负的时候，系统可能认为此activity的Intent Filter有极其苛刻的过滤条件，此activity也只能适用于少数场合，因此系统按照我们设置的优先级来发挥作用。当我们拦截广播的时候，设置receiver的Intent Filter优先级，如果不设置优先级的话，程序是会报错的，而我们只有设置成1000，程序才能正常运行，可见，1000的优先级仍然发挥作用。
#### 8.如何显式调用不同应用之间的activity
采用intent.setComponent(new ComponentName(packageName,className))函数即可。
注意className必须要包含包名，比如包名为com.android.snake，类名为SnakeMenu，那么定义为intent.setComponent(newComponentName(“com.android.snake”,”com.android.snake.SnakeMenu”))才有效，而设置为intent.setComponent(newComponentName(“com.android.snake”,” SnakeMenu”))是无效的。