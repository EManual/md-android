直接看效果图
这个是设置
```  
public int getCount() {
	return Integer.MAX_VALUE;
}
index=gallery.getCount()/2;
gallery.setSelection(iindex);
```
![img](P)  
如果这样写
```  
public int getCount() {
	//return Integer.MAX_VALUE;
	return bitmap.size();
}
// index=gallery.getCount()/2;
// gallery.setSelection(iindex);
```
显示效果就是这样的
![img](P)  
![img](P)  
可以看到在第3个倒数第3个图片层次显示不正确  中间的效果和上边的一样是正确的
不知道大家怎么解决这个问题
顺便说一下 这个主要是设置gallery.setSpacing 为负值
apidemos 如果这样设置也会有一样的问题
下面  我把关键代码给大家  里面主要是这个gallery的重写 
adapter 里面 设置MAX_VALUE 可以实现无限循环 （注意索引）
```  
public int getCount() {
	return mDatas == null ? 0 : Integer.MAX_VALUE;
}
```
activity 里面对这个gallery的设置。
```  
mGallary.setSpacing(-100);   
```        
这个值要根据图片的大小来设置 这样就可以有重叠效果。