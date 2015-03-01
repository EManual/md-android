Android中提供了Intent机制来协助应用间的交互与通讯，或者采用更准确的说法是，Intent不仅可用于应用程序之间，也可用于应用程序内部的Activity/Service之间的交互。Intent这个英语单词的本意是“目的、意向”等，对于较少从事于大型平台开发工作的程序员来说，这可能是一个不太容易理解的抽象概念，因为它与我们平常使用的简单函数/方法调用，或者上节中提到的通过库调用接口的方式不太一样。在Intent的使用中你看不到直接的函数调用，相对函数调用来说，Intent是更为抽象的概念，利用Intent所实现的软件复用的粒度是Activity/Service，比函数复用更高一些，另外耦合也更为松散。
Android中与Intent相关的还有Action/Category及Intent Filter等，另外还有用于广播的Intent，这些元素掺杂在一起，导致初学者不太容易迅速掌握Intent的用法。在讲解这些名词之前，我们先来从下面的例子中感受一下Intent的一些基本用法，看看它能做些什么，之后再来思考这种机制背后的意义。
理解Intent的关键之一是理解清楚Intent的两种基本用法：一种是显式的Intent，即在构造Intent对象时就指定接收者，这种方式与普通的函数调用类似，只是复用的粒度有所差别；另一种是隐式的Intent，即Intent的发送者在构造Intent对象时，并不知道也不关心接收者是谁，这种方式与函数调用差别比较大，有利于降低发送者和接收者之间的耦合。另外Intent除了发送外，还可用于广播，这些都将在后文进行详细讲述。
Intent和Bundle实现从一个Activity带参数转换到另一个Activity的代码例子
```  
if (et_username.getText().toString().equals("peidw")
		&& et_password.getText().toString().equals("123456")) {
	intent = new Intent();
	Bundle bundle = new Bundle();
	bundle.putString("USERNAME", et_username.getText().toString());
	intent.putExtras(bundle);
	intent.setClass(loginactive.this, informationactive.class);
	startActivity(intent);
} else {
	intent = new Intent();
	intent.setClass(loginactive.this, errorpageactive.class);
	startActivity(intent);
}
```
在另一个Activity中取出Bundle 的参数
```  
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	this.setContentView(R.layout.informationactive);
	tv = (TextView) findViewById(R.id.first_page_info);
	Bundle bundle = this.getIntent().getExtras();
	String str = bundle.getString("USERNAME");
	tv.setText(str);
	button_back = (Button) findViewById(R.id.back);
	button_back.setOnClickListener(new OnClickListener() {
		public void onClick(View view) {
			Intent intent = new Intent();
			intent.setClass(informationactive.this, mainactive.class);
			startActivity(intent);
		}
	});
}
```
看了上面的代码，我想大家也明白Intent的作用了。