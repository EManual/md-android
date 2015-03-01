因为此文件包含三个测试用例，所以我将会逐个介绍一下。
首先，需要注意的是，我们定位服务器地址时使用到了IP，因为这里不能用localhost，服务端是在windows上运行，而本单元测试运行在Android平台，如果使用localhost就意味着在Android内部去访问服务，可能是访问不到的，所以必须用IP来定位服务。
我们先来分析一下testGet测试用例.我们使用了HttpGet，请求参数直接附在URL后面，然后由HttpClient执行GET请求，如果响应成功的话，取得响应内如输入流，并转换成字符串，最后判断是否为GET_SUCCESS。
testGet测试对应服务端Servlet代码如下：
```  
@Override
protected void doGet(HttpServletRequest request,
		HttpServletResponse response) throws ServletException, IOException {
	System.out.println("doGet method is called.");
	String id = request.getParameter("id");
	String name = request.getParameter("name");
	String age = request.getParameter("age");
	System.out.println("id:" + id + ", name:" + name + ", age:" + age);
	response.getWriter().write("GET_SUCCESS");
}
```
然后再说testPost测试用例。我们使用了HttpPost，URL后面并没有附带参数信息，参数信息被包装成一个由NameValuePair类型组成的集合的形式，然后经过UrlEncodedFormEntity处理后调用HttpPost的setEntity方法进行参数设置，最后由HttpClient执行。
testPost测试对应的服务端代码如下：
```  
@Override
protected void doPost(HttpServletRequest request,
		HttpServletResponse response) throws ServletException, IOException {
	System.out.println("doPost method is called.");
	String id = request.getParameter("id");
	String name = request.getParameter("name");
	String age = request.getParameter("age");
	System.out.println("id:" + id + ", name:" + name + ", age:" + age);
	response.getWriter().write("POST_SUCCESS");
}
```
上面两个是最基本的GET请求和POST请求，参数都是文本数据类型，能满足普通的需求，不过在有的场合例如我们要用到上传文件的时候，就不能使用基本的GET请求和POST请求了，我们要使用多部件的POST请求。下面介绍一下如何使用多部件POST操作上传一个文件到服务端。
由于Android附带的HttpClient版本暂不支持多部件POST请求，所以我们需要用到一个HttpMime开源项目，该组件是专门处理与MIME类型有关的操作。因为HttpMime是包含在HttpComponents 项目中的，所以我们需要去apache官方网站下载HttpComponents，然后把其中的HttpMime。jar包放到项目中去，如图：
然后，我们观察testUpload测试用例，我们用HttpMime提供的InputStreamBody处理文件流参数，用StringBody处理普通文本参数，最后把所有类型参数都加入到一个MultipartEntity的实例中，并将这个multipartEntity设置为此次POST请求的参数实体，然后执行POST请求。服务端Servlet代码如下：
```  
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Iterator;
import java.util.List;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.FileUploadException;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
@SuppressWarnings("serial")
public class UploadServlet extends HttpServlet {
	@Override
	@SuppressWarnings("rawtypes")
	protected void doPost(HttpServletRequest request,
			HttpServletResponse response) throws ServletException, IOException {
		boolean isMultipart = ServletFileUpload.isMultipartContent(request);
		if (isMultipart) {
			FileItemFactory factory = new DiskFileItemFactory();
			ServletFileUpload upload = new ServletFileUpload(factory);
			try {
				List items = upload.parseRequest(request);
				Iterator iter = items.iterator();
				while (iter.hasNext()) {
					FileItem item = (FileItem) iter.next();
					if (item.isFormField()) {
						// 普通文本信息处理
						String paramName = item.getFieldName();
						String paramValue = item.getString();
						System.out.println(paramName + ":" + paramValue);
					} else {
						// 上传文件信息处理
						String fileName = item.getName();
						byte[] data = item.get();
						String filePath = getServletContext().getRealPath(
								"/files")
								+ "/" + fileName;
						FileOutputStream fos = new FileOutputStream(filePath);
						fos.write(data);
						fos.close();
					}
				}
			} catch (FileUploadException e) {
				e.printStackTrace();
			}
		}
		response.getWriter().write("UPLOAD_SUCCESS");
	}
}
```