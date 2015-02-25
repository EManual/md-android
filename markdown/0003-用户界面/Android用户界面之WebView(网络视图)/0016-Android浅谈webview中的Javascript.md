在webview中使用jQuery等框架，很影响网页加载速度，所以我都是使用纯Javascript来写页面脚本。在开发webview程序过程中，经常用到了一些东西，总结一下：
1排序：对一个对象数组进行排序，大的在前，小的在后
```  
var array = [{id:1,date: 1272775205971}, {id:2,date: 1272775145384}, {id:3,date: 1272774832649}] 
function sortByDate(array) { 
	array.sort(function(a, b){
		if(a.date > b.date) return -1;
		else if(a.date < b.date) return 1; 
		else return 0; 
	}); 
} 
```
2．隐藏桩节点：
页面上有如下元素， id 是 line 的 div 是一个桩节点， content 下的所有内容都是由这个桩节点 clone 出来的。
```  
<div id=’content’> 
	<div id='line'>
		<img class='type' src=''/>
		<span class='duration'>...</span>
		<span class='date'>...</span>
	</div>
</div> 
```
在所有节点clone结束之后，需要隐藏这个桩节点。其他的克隆出来的节点id也是line，没有改变，webkit下，firstChild获取的节点是textNode，所以需要进行一些判断，来确定这个桩节点。
```  
function hideStub() { 
	var stub = (function(){
		return arguments[0].nodeType == 1?arguments[0]:arguments.callee(arguments[0].nextSibling); 
	})(document.getElementById('content').firstChild); 
	stub.style.display = 'none';
} 
```
3．以前博文中提过，Webview支持 ava和javascript互调。而调用Java方法，返回的字符串不是 javascript的本地字符串。简单来说，就是 javascript的字符串和从java中获取的字符串不一样，很多字符串操作函数都不支持。需要进行一道转换，转换方法就是对它调用toLocaleString() 函数。
4．从java中获取的json字符串，在javascript中要转成json对象，一个很简单的方法就是eval(json)或window.eval(json)。我以前也一直是这么做的。昨天，我这么解析一个从java中返回的json字符串，却遇到了问题，用这个eval解析它，webkit一直报错，说语法错误。后来我用了另外一种方法，解决了。
很简单，就是构造一个函数，这个函数返回这个字符串，然后运行这个函数，就得到了json 对象。
```  
var x = (new Function('return '+ json.toLocaleString()+';'))(); 
```
5．Webview中的页面，要可拖动并且里面元素可以点击，这个问题曾困扰过我，因为事件的冒泡机制似乎并不太管用。后来还是解决了，这种方法我经常用到。
```  
<div id='log'> 
<!—- 整个log元素需要可以拖动 --> 
<div id='line'> 
	<img class='type' src=''/>
	<span class='duration'>...</span>
	<span class='date'>...</span>
</div> 
<div id='line'> 
	<img class='type' src=''/>
	<span class='duration'>...</span>
	<span class='date'>...</span>
</div> 
<!—- 很多个id是line的div，每个都可以点击 --> 
</div> 
```
Javascript:
```  
document.getElementById('log').addEventListener('touchstart', function(e){ 
	Scroll.moved = false;
	e.preventDefault();
	clearTimeout(Scroll.handler);
	showScrollBar();
	Scroll.down = true;
	Scroll.y = e.touches[0].clientY; }, false);
document.getElementById('log').addEventListener('touchmove', function(e){ 
	if(!Scroll.moved) {//没有滚动的时候，不执行move操作
		var rx = Scroll.ix - e.touches[0].clientX;
		var ry = Scroll.iy - e.touches[0].clientY;
		if(rx>-10 && rx <10 && ry>-10 && ry<10) return;//移动范围小于10*10，则认为没有滚动
		Scroll.moved = true;//否则，认为滚动了
	} 
	e.preventDefault();
	var dy = e.touches[0].clientY - Scroll.y;
	document.getElementById('log').scrollTop += -dy;
	Scroll.y = e.touches[0].clientY; }, false);
document.getElementById('log').addEventListener('touchend', function(e){ 
	e.preventDefault();
	Scroll.moved = false;
	Scroll.handler = setTimeout(hideScrollBar, 1000);  }, false);
//子节点添加点击： 
document.getElementById('line').addEventListener('touchstart', function(e){ e.preventDefault(); }, false); 
child.addEventListener('touchend', function(e){ 
	e.preventDefault();
	if(Scroll.moved) return;//页面滚动了，不执行任何操作
	//否则，在此触发点击事件，执行一些操作 
	}, false); 
//用于存储滚动的状态 
Scroll = { 
	moved:false,
	handler:null,
	down:false,
	y:0,
	ix:0,
	iy:0,
	dy:0
}
```