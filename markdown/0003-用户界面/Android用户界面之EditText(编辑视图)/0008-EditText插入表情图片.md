自定义EditText,提供插入图片的接口,给大家提供一个参考,大家可以自己完善里面的功能,加上捕获EditText键盘事件就可以实现一个完整的支持表情图片的EditText(类似QQ聊天)
提供一个小思路:删除图片,其实就是重置EditText文本. 添加图片用SpannableString ImageSpan
关键代码:
```  
SpannableString ss = new SpannableString(getText().toString()+"[smile]");  
Drawable d = getResources().getDrawable(id);
d.setBounds(0, 0, d.getIntrinsicWidth(), d.getIntrinsicHeight());  
ImageSpan span = new ImageSpan(d, ImageSpan.ALIGN_BASELINE);  
ss.setSpan(span, getText().length(),getText().length()+"[smile]".length(), Spannable.SPAN_INCLUSIVE_EXCLUSIVE);  
setText(ss);
```