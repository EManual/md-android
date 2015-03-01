代码如下：
```  
 /**
  * 保存账号和密码
  * @param isRemember
  *  是否保存账号和密码
  */
private void saveAccAndPass(boolean isRemember) {
	SharedPreferences rem_pass = FusionField.currentActivity
			.getSharedPreferences(REM_PASSWORD, Context.MODE_PRIVATE);
	SharedPreferences.Editor rem_pass_editor = rem_pass.edit();

	if (isRemember) {
		rem_pass_editor.putString(ACCOUNT, et_account.getText().toString()
				.trim());
		rem_pass_editor.putString(PASSWORD, et_password.getText()
				.toString().trim());
		rem_pass_editor.putBoolean(ISREMEMBER, true);
		rem_pass_editor.commit();
	} else {
		rem_pass_editor.putString(ACCOUNT, et_account.getText().toString()
				.trim());
		rem_pass_editor.putString(PASSWORD, "");
		rem_pass_editor.putBoolean(ISREMEMBER, false);
		rem_pass_editor.commit();
	}
}
```