login.java
```  
import android.app.Activity;
import android.os.Bundle;
public class Login extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.login);
	}
}
```
#### MD5Demo.java
```  
import java.security.MessageDigest;
import android.app.Activity;
import android.content.Intent;
import android.content.SharedPreferences;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
public class MD5Demo extends Activity {
	private EditText username, password;
	private Button savebtn, loginbtn;
	String user, pass;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		username = (EditText) findViewById(R.id.username);
		password = (EditText) findViewById(R.id.password);
		savebtn = (Button) findViewById(R.id.save);
		loginbtn = (Button) findViewById(R.id.login);
		savebtn.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				SharedPreferences pre = getSharedPreferences("loginvalue",
						MODE_WORLD_WRITEABLE);
				pass = MD5(password.getText().toString());
				user = username.getText().toString();
				if (!pass.equals("") && !user.equals("")) {
					pre.edit()
							.putString("username",
									username.getText().toString())
							.putString("password", encryptmd5(pass)).commit();
					Toast.makeText(getApplicationContext(), "保存成功!",
							Toast.LENGTH_SHORT).show();
				} else {
					Toast.makeText(getApplicationContext(), "密码不能为空！",
							Toast.LENGTH_LONG).show();
				}
			}
		});
		loginbtn.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				SharedPreferences sp = getSharedPreferences("loginvalue",
						MODE_WORLD_READABLE);
				String loginuser = sp.getString("username", null);
				String loginpass = sp.getString("password", null);
				user = username.getText().toString();
				pass = password.getText().toString();
				String passmd5 = MD5(pass);
				String encryptmd5 = encryptmd5(passmd5);
				System.out.println("username=" + loginuser
						+ "-------------password=" + loginpass);
				System.out.println("user==" + user
						+ "-------------encryptmd5==" + encryptmd5);
				if (!user.equals("") && !pass.equals("")) {
					if (user.equals(loginuser) && encryptmd5.equals(loginpass)) {
						Intent intent = new Intent();
						intent.setClass(MD5Demo.this, Login.class);
						MD5Demo.this.startActivity(intent);
						finish();
					} else {
						Toast.makeText(getApplicationContext(), "密码是错误的！",
								Toast.LENGTH_LONG).show();
					}
				} else {
					Toast.makeText(getApplicationContext(), "密码不能为空！",
							Toast.LENGTH_LONG).show();
				}
			}
		});
	}
	// MD5加密，32位
	public static String MD5(String str) {
		MessageDigest md5 = null;
		try {
			md5 = MessageDigest.getInstance("MD5");
		} catch (Exception e) {
			e.printStackTrace();
			return "";
		}
		char[] charArray = str.toCharArray();
		byte[] byteArray = new byte[charArray.length];
		for (int i = 0; i < charArray.length; i++) {
			byteArray[i] = (byte) charArray[i];
		}
		byte[] md5Bytes = md5.digest(byteArray);
		StringBuffer hexValue = new StringBuffer();
		for (int i = 0; i < md5Bytes.length; i++) {
			int val = ((int) md5Bytes[i]) & 0xff;
			if (val < 16) {
				hexValue.append("0");
			}
			hexValue.append(Integer.toHexString(val));
		}
		return hexValue.toString();
	}
	// 可逆的加密算法
	public static String encryptmd5(String str) {
		char[] a = str.toCharArray();
		for (int i = 0; i < a.length; i++) {
			a[i] = (char) (a[i] ^ 'l');
		}
		String s = new String(a);
		return s;
	}
}
```
![img](P)  
![img](P)  