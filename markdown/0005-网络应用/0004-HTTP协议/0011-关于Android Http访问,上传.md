刚开始学习android的时候，想上传图片或者文件到服务器都折腾了很久，不过在后来的开发中把它写成了一个公共类，每次用的时候只传相关参数即可，非常方便，今天写下来也方便自己以后使用。
```  
import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Map;
import java.util.Random;
import java.util.concurrent.TimeUnit;
import org.apache.http.client.HttpClient;
import org.apache.http.impl.client.DefaultHttpClient;
import com.bingo.BingoApp;
public class Communication {
	[Tags]/**
	 [Tags]* @param 只发送普通数据
	 [Tags]*            ,调用此方法
	 [Tags]* @param urlString
	 [Tags]*            对应的Php 页面
	 [Tags]* @param params
	 [Tags]*            需要发送的相关数据 包括调用的方法
	 [Tags]* @return
	 [Tags]*/
	public String communication(String urlString, Map<String, String> params) {
		HttpClient client = new DefaultHttpClient();
		client.getConnectionManager()
				.closeIdleConnections(20, TimeUnit.SECONDS);// 20秒
		String result = "";
		String uploadUrl = new BingoApp().URLIN + "/";// new BingoApp().URLIN
		// http://192.168.10.9/bingo/Server/code
		String MULTIPART_FORM_DATA = "multipart/form-data";
		String BOUNDARY = "---------7d4a6d158c9"; // 数据分隔线
		if (!urlString.equals("")) {
			uploadUrl = uploadUrl + urlString;
			try {
				URL url = new URL(uploadUrl);
				HttpURLConnection conn = (HttpURLConnection) url
						.openConnection();
				conn.setDoInput(true);// 允许输入
				conn.setDoOutput(true);// 允许输出
				conn.setUseCaches(false);// 不使用Cache
				conn.setConnectTimeout(6000);// 6秒钟连接超时
				conn.setReadTimeout(25000);// 25秒钟读数据超时
				conn.setRequestMethod("POST");
				conn.setRequestProperty("Connection", "Keep-Alive");
				conn.setRequestProperty("Charset", "UTF-8");
				conn.setRequestProperty("Content-Type", MULTIPART_FORM_DATA
						+ "; boundary=" + BOUNDARY);
				StringBuilder sb = new StringBuilder();
				// 上传的表单参数部分，格式请参考文章
				for (Map.Entry<String, String> entry : params.entrySet()) {// 构建表单字段内容
					sb.append("--");
					sb.append(BOUNDARY);
					sb.append("\r\n");
					sb.append("Content-Disposition: form-data; name=\
							+ entry.getKey() + "\"\r\n\r\n");
					sb.append(entry.getValue());
					sb.append("\r\n");
				}
				DataOutputStream dos = new DataOutputStream(
						conn.getOutputStream());
				dos.write(sb.toString().getBytes());
				dos.writeBytes("--" + BOUNDARY + "--\r\n");
				dos.flush();
				InputStream is = conn.getInputStream();
				InputStreamReader isr = new InputStreamReader(is, "utf-8");
				BufferedReader br = new BufferedReader(isr);
				result = br.readLine();
			} catch (Exception e) {
				result = "{\"ret\":\"898\"}";
			}
		}
		return result;
	}
	[Tags]/**
	 [Tags]* @param 只发送普通数据
	 [Tags]*            ,调用此方法
	 [Tags]* @param urlString
	 [Tags]*            对应的Php 页面
	 [Tags]* @param params
	 [Tags]*            需要发送的相关数据 包括调用的方法
	 [Tags]* @paramimage 图片字节数组或者文件字节数组
	 [Tags]* @paramimg 图片名称
	 [Tags]* @return Json
	 [Tags]*/
	public String communication01(String urlString, Map<String, Object> params,
			byte[] image, String img) {
		String result = "";
		String end = "\r\n";
		String uploadUrl = new BingoApp().URLIN + "/";// new BingoApp().URLIN
		String MULTIPART_FORM_DATA = "multipart/form-data";
		String BOUNDARY = "---------7d4a6d158c9"; // 数据分隔线
		String imguri = "";
		Random random = new Random();
		int temp = random.nextInt();
		imguri = temp + "sdfse" + ".jpg";
		if (!urlString.equals("")) {
			uploadUrl = uploadUrl + urlString;
			try {
				URL url = new URL(uploadUrl);
				HttpURLConnection conn = (HttpURLConnection) url
						.openConnection();
				conn.setDoInput(true);// 允许输入
				conn.setDoOutput(true);// 允许输出
				conn.setUseCaches(false);// 不使用Cache
				conn.setConnectTimeout(6000);// 6秒钟连接超时
				conn.setReadTimeout(6000);// 6秒钟读数据超时
				conn.setRequestMethod("POST");
				conn.setRequestProperty("Connection", "Keep-Alive");
				conn.setRequestProperty("Charset", "UTF-8");
				conn.setRequestProperty("Content-Type", MULTIPART_FORM_DATA
						+ "; boundary=" + BOUNDARY);
				StringBuilder sb = new StringBuilder();
				// 上传的表单参数部分，格式请参考文章
				for (Map.Entry<String, Object> entry : params.entrySet()) {// 构建表单字段内容
					sb.append("--");
					sb.append(BOUNDARY);
					sb.append("\r\n");
					sb.append("Content-Disposition: form-data; name=\
							+ entry.getKey() + "\"\r\n\r\n");
					sb.append(entry.getValue());
					sb.append("\r\n");
				}
				sb.append("--");
				sb.append(BOUNDARY);
				sb.append("\r\n");
				DataOutputStream dos = new DataOutputStream(
						conn.getOutputStream());
				dos.write(sb.toString().getBytes());
				if (!imguri.equals("") && !imguri.equals(null)) {
					dos.writeBytes("Content-Disposition: form-data; name=\
							+ img + "\"; filename=\"" + imguri + "\"" + "\r\n
							+ "Content-Type: image/jpeg\r\n\r\n");
					dos.write(image, 0, image.length);
					dos.writeBytes(end);
					dos.writeBytes("--" + BOUNDARY + "--\r\n");
					dos.flush();
					InputStream is = conn.getInputStream();
					InputStreamReader isr = new InputStreamReader(is, "utf-8");
					BufferedReader br = new BufferedReader(isr);
					result = br.readLine();
				}
			} catch (Exception e) {
				result = "{\"ret\":\"898\"}";
			}
		}
		return result;
	}
	[Tags]/**
	 [Tags]* @param 只发送普通数据
	 [Tags]*            ,调用此方法
	 [Tags]* @param urlString
	 [Tags]*            对应的Php 页面
	 [Tags]* @param params
	 [Tags]*            需要发送的相关数据 包括调用的方法
	 [Tags]* @param imageuri
	 [Tags]*            图片或文件手机上的地址 如:sdcard/photo/123.jpg
	 [Tags]* @param img
	 [Tags]*            图片名称
	 [Tags]* @return Json
	 [Tags]*/
	public String communication02(String urlString, Map<String, Object> params,
			String imageuri, String img) {
		String result = "";
		String end = "\r\n";
		String uploadUrl = new BingoApp().URLIN + "/";// new BingoApp().URLIN
		String MULTIPART_FORM_DATA = "multipart/form-data";
		String BOUNDARY = "---------7d4a6d158c9"; // 数据分隔线
		String imguri = "";
		if (!imageuri.equals("")) {
			imguri = imageuri.substring(imageuri.lastIndexOf("/") + 1);// 获得图片或文件名称
		}
		if (!urlString.equals("")) {
			uploadUrl = uploadUrl + urlString;
			try {
				URL url = new URL(uploadUrl);
				HttpURLConnection conn = (HttpURLConnection) url
						.openConnection();
				conn.setDoInput(true);// 允许输入
				conn.setDoOutput(true);// 允许输出
				conn.setUseCaches(false);// 不使用Cache
				conn.setConnectTimeout(6000);// 6秒钟连接超时
				conn.setReadTimeout(6000);// 6秒钟读数据超时
				conn.setRequestMethod("POST");
				conn.setRequestProperty("Connection", "Keep-Alive");
				conn.setRequestProperty("Charset", "UTF-8");
				conn.setRequestProperty("Content-Type", MULTIPART_FORM_DATA
						+ "; boundary=" + BOUNDARY);
				StringBuilder sb = new StringBuilder();
				// 上传的表单参数部分，格式请参考文章
				for (Map.Entry<String, Object> entry : params.entrySet()) {// 构建表单字段内容
					sb.append("--");
					sb.append(BOUNDARY);
					sb.append("\r\n");
					sb.append("Content-Disposition: form-data; name=\
							+ entry.getKey() + "\"\r\n\r\n");
					sb.append(entry.getValue());
					sb.append("\r\n");
				}
				sb.append("--");
				sb.append(BOUNDARY);
				sb.append("\r\n");
				DataOutputStream dos = new DataOutputStream(
						conn.getOutputStream());
				dos.write(sb.toString().getBytes());
				if (!imageuri.equals("") && !imageuri.equals(null)) {
					dos.writeBytes("Content-Disposition: form-data; name=\
							+ img + "\"; filename=\"" + imguri + "\"" + "\r\n
							+ "Content-Type: image/jpeg\r\n\r\n");
					FileInputStream fis = new FileInputStream(imageuri);
					byte[] buffer = new byte[1024]; // 8k
					int count = 0;
					while ((count = fis.read(buffer)) != -1) {
						dos.write(buffer, 0, count);
					}
					dos.writeBytes(end);
					fis.close();
				}
				dos.writeBytes("--" + BOUNDARY + "--\r\n");
				dos.flush();
				InputStream is = conn.getInputStream();
				InputStreamReader isr = new InputStreamReader(is, "utf-8");
				BufferedReader br = new BufferedReader(isr);
				result = br.readLine();
			} catch (Exception e) {
				result = "{\"ret\":\"898\"}";
			}
		}
		return result;
	}
}
```