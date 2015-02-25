activity的启动模式有哪些？是什么含义？
答：在android里，有4种activity的启动模式，分别为：
```   
“standard” (默认) 
“singleTop” 
“singleTask” 
“singleInstance”
```
它们主要有如下不同：
#### 1. 如何决定所属task 
“standard”和”singleTop”的activity的目标task，和收到的Intent的发送者在同一个task内，除非intent包括参数FLAG_ACTIVITY_NEW_TASK。 
如果提供了FLAG_ACTIVITY_NEW_TASK参数，会启动到别的task里。 
“singleTask”和”singleInstance”总是把activity作为一个task的根元素，他们不会被启动到一个其他task里。
#### 2. 是否允许多个实例 
“standard”和”singleTop”可以被实例化多次，并且存在于不同的task中，且一个task可以包括一个activity的多个实例； 
“singleTask”和”singleInstance”则限制只生成一个实例，并且是task的根元素。 singleTop要求如果创建intent的时候栈顶已经有要创建 的Activity的实例，则将intent发送给该实例，而不发送给新的实例。
#### 3. 是否允许其它activity存在于本task内 
“singleInstance”独占一个task，其它activity不能存在那个task里；如果它启动了一个新的activity，不管新的activity的launch mode 如何，新的activity都将会到别的task里运行（如同加了FLAG_ACTIVITY_NEW_TASK参数）。 
而另外三种模式，则可以和其它activity共存。
#### 4. 是否每次都生成新实例 
“standard”对于没一个启动Intent都会生成一个activity的新实例； 
“singleTop”的activity如果在task的栈顶的话，则不生成新的该activity的实例，直接使用栈顶的实例，否则，生成该activity的实例。 
比如现在task栈元素为A-B-C-D（D在栈顶），这时候给D发一个启动intent，如果D是“standard”的，则生成D的一个新实例，栈变为A－B－C－D－D。 
如果D是singleTop的话，则不会生产D的新实例，栈状态仍为A-B-C-D 
如果这时候给B发Intent的话，不管B的launchmode是”standard” 还是 “singleTop” ，都会生成B的新实例，栈状态变为A-B-C-D-B。
“singleInstance”是其所在栈的唯一activity，它会每次都被重用。
“singleTask”如果在栈顶，则接受intent，否则，该intent会被丢弃，但是该task仍会回到前台。
当已经存在的activity实例处理新的intent时候，会调用onNewIntent()方法 
如果收到intent生成一个activity实例，那么用户可以通过back键回到上一个状态；如果是已经存在的一个activity来处理这个intent的话，用户不能通过按back键返回到这之前的状态。
