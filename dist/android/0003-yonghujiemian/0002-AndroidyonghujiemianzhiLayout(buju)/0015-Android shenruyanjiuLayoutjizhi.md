Layout对于迅速的搭建界面和提高界面在不同分辨率的屏幕上的适应性具有很大的作用。这里简要介绍Android的Layout和研究一下它的实现。
Android有4种 Layout：FrameLayout，LinearLayout，TableLayout，RelativeLayout。
放入Layout中进行排布的View的XML属性：
4 种Layout中Item所共有的XML属性：
```  
(1)layout_width
(2)layout_height
(3)layout_marginLeft
(4)layout_marginTop
(5)layout_marginRight
(6)layout_marginBottom
(7)layout_gravity
//FrameLayout中的Item就具有这些属性。
//对于LinearLayout还会有
(8)layout_weight
//TableLayout的行TableRow是一个横向的(horizontal)的LinearLayout。
//RelativeLayout有16个align相关的 XML属性。
(9)layout_above
(10)layout_alignBaseline
(11)layout_alignBottom
(12)layout_alignLeft
(13)layout_alignParentBottom
(14)layout_alignParentLeft
(15)layout_alignParentRight
(16)layout_alignParentTop
(17)layout_alignRight
(18)layout_alignTop
(19)layout_below
(20)layout_centerHorizontal
(21)layout_centerInParent
(22)layout_centerVertical
(23)layout_toLeftOf
(24)layout_toRightOf
```
(1)和(2)用来确定放入Layout中的View的宽度和高度：它们的可能取值为fill_parent，wrap_content或者固定的像素值。(3)(4)(5)(6)是放入Layout 中的View期望它能够和Layout的边界或者其他View之间能够相距一段距离。
(7)用来确定View在Layout中的停靠位置。
(8) 用于在LinearLayout中把所有子View排布之后的剩余空间按照它们的layout_weight分配给各个拥有这个属性的View。
(9) 到(24)用来确定RelativeLayout中的View相对于Layout或者Layout中的其他View的位置。
根据Android的文档，Android会对Layout和View嵌套组成的这棵树进行2次遍历，一次是measure调用，用来确定Layout或者View的大小;一次是layout调用，用来确定Layout或者view的位置。当然后来我自己的山寨实现把这2次调用合并到了一起。那就是Layout在排布之前都对自己进行measure一次，然后对View递归调用Layout方法。这样子的大小肯定是确定了的。然后用确定了的大小来使用gravity或者align属性来定位，使用 margin来调整位置。伪代码如下所示：
```  
long layout(parent_leftPos, parent_topPos, parent_width, parent_height)
{
	measure(parent_width, parent_height);
	while (!list.empty())
	{
	horizontalLeft = horizontalLeft - pChild->leftMargin - pChild->rightMargin;
	verticalLeft = verticalLeft - pChild->topMargin - pChild->bottomMargin;
	pChild->layout(curLeftPos, curTopPos, horizontalLeft, verticalLeft);
	// use gravity or align
	// use margin
	// scale layout size if this is a layout and it's layout_width or layout_height is wrap_content
	}
// use weight.
}
```
如果自己来实现这套机制的话，实现的难点就是根据用户对View或者Layout指定的layout_width，layout_height的三种值来确定View或者Layout的大小。
对于View来说这个比较容易。
(1)它下面没有嵌套的Layout的时候：比如如果它是一个标准控件Button，它的layout_width是WRAP_CONTENT或者像素值的话，那么就是确定的了。如果layout_width是FILL_PARENT，它所在的Layout如果是FILL_PARENT，也是确定了的。如果是WRAP_CONTENT，那么这个是一个属性冲突，可以依照Layout的属性来进行处理。
(2)它下面嵌套有Layout的时候，那么这个就涉及到了Layout的大小确定了，这转化为了Layout大小确定问题。
对于Layout来说比较复杂。
主要Layout之间可以相互嵌套。如果父Layout是WRAP_CONTENT，子Layout是FILL_CONTENT，而子Layout的content则是它下面的各个View排布完成之后才可以确定的，所以View的冲突处理方法不能直接使用。也就是说measure的调用过程中，如果不对View进行排布的话，就不能得到Layout的 content大小。那么既然不排布不知道，那就在measure的时候把WRAP_CONTENT属性保留到layout调用的时候再确定吧。
在实现Layout的measure方法的时候，先要尽可能(因为WRAP_CONTENT的原因)确定自己本身的measuredWidth和measuredHeight，然后传给各个子View或者Layout进行measure调用。在实现Layout的layout方法的时候，则要先对各个子View或者Layout进行layout调用，然后才是对Layout本身根据Layout本身的性质和Layout中各个View的属性来进行排布。这样做的目的是先排布子节点，获得子节点的大小(逐层递归调用下去，最后达到View子节点的时候就全部确定了，然后逐层递归返回，上层节点也都确定了measuredWidth和 measuredHeight)。
最后有一点要说明的就是Layout在排布它的子View或者Layout的时候，由于自己的大小还没有确定，所以有些View的layout属性即使设置了，也是要忽略的。比如Android文档上提到：如果你对RelativeLayout的layout_width设置为WRAP_CONTENT，而对它下面的一个View设置了属性layout_alignParentRight为true，这就是冲突的情况。所以用户在使用这些layout属性之前，首先要确保它们逻辑上都是行的通的，然后才能期望Layout机制能够正确的起作用。
当然Android的真实实现过程当然比上面的描述的要复杂的多，但是通过Eclipse上 Android插件反复的试验，也可以看出Android的实现也并非是完美无瑕的，它也有赖于用户的正确使用。逻辑不通的，相互冲突的layout属性也会使它的行为难以预测。