3.创建一个Java类文件，导入以下包：
```  
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import com.eshore.smsManagers.R;
import android.app.Activity;
import android.telephony.gsm.*;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import android.os.Bundle;
[/coide]
4.重写onCreate方：
```  
txtFrom=(EditText)this.findViewById(R.id.txt_from);
txtContent=(EditText)this.findViewById(R.id.txt_content);
btnSend=(Button)this.findViewById(R.id.btnSend);
btnSend.setOnClickListener(new OnClickListener() {
	public void onClick(View v) {
		if(!validate())
			return;
		SmsManager.getDefault().sendTextMessage(txtFrom.getText().toString().tri(),null,txtContent.getText().toString(),null,null);
		txtFrom.setText("");
		txtContent.setText("");
		Toast toast=Toast.makeText(main.this, "短信发送成功!",Toast.LENGTH_LONG);
		toast.show();
	}
});
```
相关的辅助方法有手机的合法性验证和短信内容不可为空：
```  
//合法性验证
private boolean validate(){
	String mobile=txtFrom.getText().toString().trim();
	String content=txtContent.getText().toString();
	if(mobile.equals("")){
		Toast toast=Toast.makeText(this, "手机号码不能为空!",Toast.LENGTH_LONG);
		toast.show();
		return false;
	} else if(!checkMobile(mobile)){
		Toast toast=Toast.makeText(this, "您输入的电话号码不正确请重新输入!",Toast.LENGTH_LONG);
		toast.show();
		return false;
	}
	else if(content.equals("")){
		Toast toast=Toast.makeText(this, "短信内容不能为空请重新输入!",Toast.LENGTH_LONG);
		toast.show();
		return false;
	}
	else{
		return true;
	}
}
/*
[Tags]*中国移动134.135.136.137.138.139.150.151.152.157.158.159.187.188 ,147(数据卡不验证)
[Tags]*中国联通130.131.132.155.156.185.186
[Tags]*中国电信133.153.180.189
CDMA 133,153
手机号码验证 适合目前所有的手机
[Tags]*/
public boolean checkMobile(String mobile){
	String regex="^1(3[0-9]|5[012356789]|8[0789])\d{8}$";
	Pattern p = Pattern.compile(regex);
	Matcher m = p.matcher(mobile);
	return m.find();
}
```