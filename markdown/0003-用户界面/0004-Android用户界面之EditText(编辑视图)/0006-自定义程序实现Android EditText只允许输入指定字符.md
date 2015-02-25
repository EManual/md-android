程序设计的很多地方都要用到一个小技术：指定文本框的输入类型。即限制只能输入某几类或某类字符，甚至是某几个字符。
Android本身已经做了很多设计，如限制长度，限制只能输入整数或数字。
有时候这些还是不够的。我们可以在程序中根据需要自己定制。
主要涉及：EditText.addTextChangedListener，EditText.removeTextChangedListener，EditText.setFilters。
方法：
对EditText添加自定义的TextChange监听。在改监听中检测输入字符是否合法。
关键代码如下：
```  
@Override
public void afterTextChanged(Editable s) {
	String str = s.toString();
	if (str.equals(tmp)) {
		return;// 如果tmp==str则返回，因为这是我们设置的结果。否则会形成死循环。
	}
	StringBuffer sb = new StringBuffer();
	for (int i = 0; i < str.length(); i++) {
		if (digits.indexOf(str.charAt(i)) >= 0) {// 判断字符是否在可以输入的字符串中
			sb.append(str.charAt(i));// 如果是，就添加到结果里，否则跳过
		}
	}
	tmp = sb.toString();// 设置tmp，因为下面一句还会导致该事件被触发
	editText.setText(tmp);// 设置结果
	editText.invalidate();
}
```
运行结果如下：
![img](P)  
![img](P)  
![img](P)  