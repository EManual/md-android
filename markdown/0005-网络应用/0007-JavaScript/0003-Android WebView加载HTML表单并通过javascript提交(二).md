java代码：
```  
new AlertDialog.Builder(WebViewTest.this) .setTitle("提示信息") .setMessage(message) .setPositiveButton("确定", new DialogInterface.OnClickListener() { 
	public void onClick( DialogInterface dialoginterface, int i) { } }).show(); 
	return true; 
	} 
	} 
	private String createWebForm(){ StringBuffer sb = new StringBuffer(); 
	sb.append("< html>").append("< head>");
	sb.append("< meta http-equiv=\"Content-Type\" content=\"text/html; charset=UTF-8\"/>"); sb.append("< title>").append("表单测试").append("< /title>");
	sb.append("< /head>< script language=\"javascript\">");
	sb.append("function checkform(){var username=document.loginForm.username.value;"); sb.append("var password=document.loginForm.password.value;");
	sb.append("if(username==\"\"){alert(\"用户名不能为空!\");
	return false
	;}");
	sb.append("if(password==\"\"){alert(\"密码不能为空!\");
	return false;}"); 
	//sb.append("return username + \":\" + password;"); 
	sb.append("window.loginImpl.login(username, password)");
	sb.append("}");
	sb.append("< /script>");
	sb.append("< body>");
	sb.append("< form method=\"post\" name=\"loginForm\">");
	sb.append("< table>");
	sb.append("< tr>");
	sb.append("< td align=\"right\">").append("用户名").append("< /td>");
	sb.append("< td").append("< input type=\"text\" name=\"username\">").append("< /td>"); sb.append("< /tr>");
	sb.append("< tr>");
	sb.append("< td align=\"right\">").append("密 码").append("< /td>");
	sb.append("< td").append("< input type=\"password\" name=\"password\">").append("< /td>"); sb.append("< /tr>");
	sb.append("< tr>");
	sb.append("< td align=\"center\" colspan=\"2\">");
	sb.append("< input type=\"submit\" value=\"登录\" onclick=\"checkform();\">");
	sb.append(" < input type=\"reset\" value=\"重置\">").append("< /td>");
	sb.append("</tr>"); sb.append("< /table>"); sb.append("< /form>");
	sb.append("< /body>");
	sb.append("< /html>");
	return sb.toString(); 
	} 
} 
```