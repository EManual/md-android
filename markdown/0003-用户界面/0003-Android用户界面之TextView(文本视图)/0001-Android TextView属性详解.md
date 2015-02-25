一、TextView的API 中文文档
#### 1.1结构
```  
java.lang.Object
android.view.View
android.widget.TextView
```
直接子类：
```  
Button, CheckedTextView, Chronometer, DigitalClock, EditText
```
间接子类：
```  
AutoCompleteTextView, CheckBox, CompoundButton, ExtractEditText,MultiAutoCompleteTextView, RadioButton, ToggleButton
```
#### 1.2API
属性名称描述
android:autoLink设置是否当文本为URL链接/email/电话号码/map时，文本显示为可点击的链接。可选值(none /web/email/phone/map/all)
android:autoText如果设置，将自动执行输入值的拼写纠正。此处无效果，在显示输入法并输入的时候起作用。
android:bufferType指定getText()方式取得的文本类别。选项editable类似于StringBuilder可追加字符，也就是说getText后可调用append方法设置文本内容。spannable 则可在给定的字符区域使用样式，参见这里1、这里2。
android:capitalize设置英文字母大写类型。此处无效果，需要弹出输入法才能看得到，参见EditView此属性说明。
android:cursorVisible设定光标为显示/隐藏，默认显示。
android:digits设置允许输入哪些字符。如“1234567890.+-*/% ()”
android:drawableBottom在text的下方输出一个drawable，如图片。如果指定一个颜色的话会把text的背景设为该颜色，并且同时和background使用时覆盖后者。
android:drawableLeft在text的左边输出一个drawable，如图片。
android:drawablePadding设置text与drawable(图片)的间隔，与drawableLeft、 drawableRight、drawableTop、drawableBottom一起使用，可设置为负数，单独使用没有效果。
android:drawableRight在text的右边输出一个drawable。 
android:drawableTop在text的正上方输出一个drawable。
android:editable设置是否可编辑。
android:editorExtras设置文本的额外的输入数据。
android:ellipsize设置当文字过长时,该控件该如何显示。有如下值设置：”start”—?省略号显示在开头;”end”—— 省略号显示在结尾;”middle”—-省略号显示在中间;”marquee” ——以跑马灯的方式显示(动画横向移动)
android:freezesText设置保存文本的内容以及光标的位置。
android:gravity设置文本位置，如设置成“center”，文本将居中显示。
android:hintText为空时显示的文字提示信息，可通过textColorHint设置提示信息的颜色。此属性在EditView 中使用，但是这里也可以用。
android:imeOptions附加功能，设置右下角IME动作与编辑框相关的动作，如actionDone右下角将显示一个“完成”，而不设置默认是一个回车符号。这个在EditView中再详细说明，此处无用。
android:imeActionId设置IME动作ID。
android:imeActionLabel设置IME动作标签。
android:includeFontPadding设置文本是否包含顶部和底部额外空白，默认为true。
android:inputMethod为文本指定输入法，需要完全限定名(完整的包名)。例如：com.google.android.inputmethod.pinyin，但是这里报错找不到。
android:inputType设置文本的类型，用于帮助输入法显示合适的键盘类型。在EditView中再详细说明，这里无效果。
android:linksClickable设置链接是否点击连接，即使设置了autoLink。
android:marqueeRepeatLimit在ellipsize指定marquee的情况下，设置重复滚动的次数，当设置为 marquee_forever时表示无限次。
android:ems设置TextView的宽度为N个字符的宽度。这里测试为一个汉字字符宽度
android:maxEms设置TextView的宽度为最长为N个字符的宽度。与ems同时使用时覆盖ems选项。
android:minEms设置TextView的宽度为最短为N个字符的宽度。与ems同时使用时覆盖ems选项。
android:maxLength限制显示的文本长度，超出部分不显示。
android:lines设置文本的行数，设置两行就显示两行，即使第二行没有数据。
android:maxLines设置文本的最大显示行数，与width或者layout_width结合使用，超出部分自动换行，超出行数将不显示。
android:minLines设置文本的最小行数，与lines类似。
android:lineSpacingExtra设置行间距。
android:lineSpacingMultiplier设置行间距的倍数。如”1.2”
android:numeric如果被设置，该TextView有一个数字输入法。此处无用，设置后唯一效果是TextView有点击效果，此属性在EdtiView将详细说明。
android:password以小点”.”显示文本
android:phoneNumber设置为电话号码的输入方式。
android:privateImeOptions设置输入法选项，此处无用，在EditText将进一步讨论。
android:scrollHorizontally设置文本超出TextView的宽度的情况下，是否出现横拉条。
android:selectAllOnFocus如果文本是可选择的，让他获取焦点而不是将光标移动为文本的开始位置或者末尾位置。 TextView中设置后无效果。
android:shadowColor指定文本阴影的颜色，需要与shadowRadius一起使用。
android:shadowDx设置阴影横向坐标开始位置。 
android:shadowDy设置阴影纵向坐标开始位置。
android:shadowRadius设置阴影的半径。设置为0.1就变成字体的颜色了，一般设置为3.0的效果比较好。
android:singleLine设置单行显示。如果和layout_width一起使用，当文本不能全部显示时，后面用“…”来表示。如 android:text="test_ singleLine " android:singleLine="true" android:layout_width="20dp"将只显示“t…”。如果不设置singleLine或者设置为false，文本将自动换行
android:text设置显示文本.
android:textAppearance设置文字外观。如 “?android:attr/textAppearanceLargeInverse”这里引用的是系统自带的一个外观，?表示系统是否有这种外观，否则使用默认的外观。可设置的值如下：textAppearanceButton/textAppearanceInverse /textAppearanceLarge/textAppearanceLargeInverse/textAppearanceMedium/textAppearanceMediumInverse/textAppearanceSmall/textAppearanceSmallInverse
android:textColor设置文本颜色
android:textColorHighlight被选中文字的底色，默认为蓝色
android:textColorHint设置提示信息文字的颜色，默认为灰色。与hint一起使用。
android:textColorLink文字链接的颜色.
android:textScaleX设置文字之间间隔，默认为1.0f。
android:textSize设置文字大小，推荐度量单位”sp”，如”15sp”
android:textStyle设置字形[bold(粗体) 0, italic(斜体) 1, bolditalic(又粗又斜) 2] 可以设置一个或多个，用“|”隔开
android:typeface设置文本字体，必须是以下常量值之一：normal 0, sans 1, serif 2, monospace(等宽字体) 3]
android:height设置文本区域的高度，支持度量单位：px(像素)/dp/sp/in/mm(毫米)
android:maxHeight设置文本区域的最大高度
android:minHeight设置文本区域的最小高度
android:width设置文本区域的宽度，支持度量单位：px(像素)/dp/sp/in/mm(毫米)，与layout_width的区别看这里。
android:maxWidth设置文本区域的最大宽度
android:minWidth设置文本区域的最小宽度