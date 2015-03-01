我们大家都知道Android 2.2的JIT性能有了本质的提高，不过对于老版本的程序提高Java执行效率还有很多语言特点来说，对于Java 1.5之后将会有明显的改进。下面的例子来自SDK:
```  
static class Foo {
	int mSplat;
}
Foo[] mArray = ...
//上面的静态类Foo的执行效果和性能，我们分三个方法zero、one和two来做对比。
public void zero() { //大多数人可能简单直接这样写
	int sum = 0;
	for (int i = 0; i < mArray.length; ++i) {
		sum += mArray[i].mSplat;
	}
}
public void one() { //通过本地对象改进性能
	int sum = 0;
	Foo[] localArray = mArray;
	int len = localArray.length;
	for (int i = 0; i < len; ++i) {
		sum += localArray[i].mSplat;
	}
}
public void two() { //推荐的方法，通过Java 1.5的新语法特性可以大幅改进性能
	int sum = 0;
	for (Foo a : mArray) {
		sum += a.mSplat;
	}
}
```
zero()最慢、one()较快、two()最快，希望这些对大家有一些的帮助。