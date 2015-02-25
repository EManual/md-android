XMLRPCClient.java
```  
import java.io.InputStreamReader;
import java.io.Reader;
import java.io.StringWriter;
import java.net.URI;
import java.net.URL;
import java.util.Map;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.HttpStatus;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.params.HttpParams;
import org.apache.http.params.HttpProtocolParams;
import org.xmlpull.v1.XmlPullParser;
import org.xmlpull.v1.XmlPullParserFactory;
import org.xmlpull.v1.XmlSerializer;
import android.util.Xml;
[Tags]/**
[Tags]* XMLRPCClient allows to call remote XMLRPC method.
[Tags]* <p>
[Tags]* The following table shows how XML-RPC types are mapped to java call parameters/response values.
[Tags]* </p>
[Tags]* <p>
[Tags]* <table border="2" align="center" cellpadding="5">
[Tags]* <thead><tr><th>XML-RPC Type</th><th>Call Parameters</th><th>Call Response</th></tr></thead>
[Tags]* <tbody>
[Tags]* <td>int, i4</td><td>byte<br />Byte<br />short<br />Short<br />int<br />Integer</td><td>int<br />Integer</td>
[Tags]* </tr>
[Tags]* <tr>
[Tags]* <td>i8</td><td>long<br />Long</td><td>long<br />Long</td>
[Tags]* </tr>
[Tags]* <tr>
[Tags]* <td>double</td><td>float<br />Float<br />double<br />Double</td><td>double<br />Double</td>
[Tags]* </tr>
[Tags]* <tr>
[Tags]* <td>string</td><td>String</td><td>String</td>
[Tags]* </tr>
[Tags]* <tr>
[Tags]* <td>boolean</td><td>boolean<br />Boolean</td><td>boolean<br />Boolean</td>
[Tags]* </tr>
[Tags]* <tr>
[Tags]* <td>dateTime.iso8601</td><td>java.util.Date<br />java.util.Calendar</td><td>java.util.Date</td>
[Tags]* </tr>
[Tags]* <tr>
[Tags]* <td>base64</td><td>byte[]</td><td>byte[]</td>
[Tags]* </tr>
[Tags]* <tr>
[Tags]* <td>array</td><td>java.util.List<Object><br />Object[]</td><td>Object[]</td>
[Tags]* </tr>
[Tags]* <tr>
[Tags]* <td>struct</td><td>java.util.Map<String, Object></td><td>java.util.Map<String, Object></td>
[Tags]* </tr>
[Tags]* </tbody>
[Tags]* </table>
[Tags]* </p>
[Tags]*/
public class XMLRPCClient {
	private static final String TAG_METHOD_CALL = "methodCall";
	private static final String TAG_METHOD_NAME = "methodName";
	private static final String TAG_METHOD_RESPONSE = "methodResponse";
	private static final String TAG_PARAMS = "params";
	private static final String TAG_PARAM = "param";
	private static final String TAG_FAULT = "fault";
	private static final String TAG_FAULT_CODE = "faultCode";
	private static final String TAG_FAULT_STRING = "faultString";
	private HttpClient client;
	private HttpPost postMethod;
	private XmlSerializer serializer;
	private HttpParams httpParams;
	[Tags]/**
	 [Tags]* XMLRPCClient constructor. Creates new instance based on server URI
	 [Tags]* @param XMLRPC
	 [Tags]*            server URI
	 [Tags]*/
	public XMLRPCClient(URI uri) {
		postMethod = new HttpPost(uri);
		postMethod.addHeader("Content-Type", "text/xml");
		// WARNING
		// I had to disable "Expect: 100-Continue" header since I had
		// two second delay between sending http POST request and POST body
		httpParams = postMethod.getParams();
		HttpProtocolParams.setUseExpectContinue(httpParams, false);

		client = new DefaultHttpClient();
		serializer = Xml.newSerializer();
	}
	[Tags]/**
	 [Tags]* Convenience constructor. Creates new instance based on server String
	 [Tags]* address
	 [Tags]* @param XMLRPC
	 [Tags]*            server address
	 [Tags]*/
	public XMLRPCClient(String url) {
		this(URI.create(url));
	}
	[Tags]/**
	 [Tags]* Convenience XMLRPCClient constructor. Creates new instance based on
	 [Tags]* server URL
	 [Tags]* @param XMLRPC
	 [Tags]*            server URL
	 [Tags]*/
	public XMLRPCClient(URL url) {
		this(URI.create(url.toExternalForm()));
	}
	[Tags]/**
	 [Tags]* Call method with optional parameters. This is general method. If you want
	 [Tags]* to call your method with 0-8 parameters, you can use more convenience
	 [Tags]* call methods
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param params
	 [Tags]*            parameters to pass to method (may be null if method has no
	 [Tags]*            parameters)
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object[] params) throws XMLRPCException {
		return callXMLRPC(method, params);
	}
	[Tags]/**
	 [Tags]* Convenience method call with no parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method) throws XMLRPCException {
		return callXMLRPC(method, null);
	}

	[Tags]/**
	 [Tags]* Convenience method call with one parameter
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param p0
	 [Tags]*            method's parameter
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object p0) throws XMLRPCException {
		Object[] params = { p0, };
		return callXMLRPC(method, params);
	}
	[Tags]/**
	 [Tags]* Convenience method call with two parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param p0
	 [Tags]*            method's 1st parameter
	 [Tags]* @param p1
	 [Tags]*            method's 2nd parameter
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object p0, Object p1)
			throws XMLRPCException {
		Object[] params = { p0, p1, };
		return callXMLRPC(method, params);
	}

	[Tags]/**
	 [Tags]* Convenience method call with three parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param p0
	 [Tags]*            method's 1st parameter
	 [Tags]* @param p1
	 [Tags]*            method's 2nd parameter
	 [Tags]* @param p2
	 [Tags]*            method's 3rd parameter
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object p0, Object p1, Object p2)
			throws XMLRPCException {
		Object[] params = { p0, p1, p2, };
		return callXMLRPC(method, params);
	}
	[Tags]/**
	 [Tags]* Convenience method call with four parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param p0
	 [Tags]*            method's 1st parameter
	 [Tags]* @param p1
	 [Tags]*            method's 2nd parameter
	 [Tags]* @param p2
	 [Tags]*            method's 3rd parameter
	 [Tags]* @param p3
	 [Tags]*            method's 4th parameter
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object p0, Object p1, Object p2, Object p3)
			throws XMLRPCException {
		Object[] params = { p0, p1, p2, p3, };
		return callXMLRPC(method, params);
	}
	[Tags]/**
	 [Tags]* Convenience method call with five parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param p0
	 [Tags]*            method's 1st parameter
	 [Tags]* @param p1
	 [Tags]*            method's 2nd parameter
	 [Tags]* @param p2
	 [Tags]*            method's 3rd parameter
	 [Tags]* @param p3
	 [Tags]*            method's 4th parameter
	 [Tags]* @param p4
	 [Tags]*            method's 5th parameter
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object p0, Object p1, Object p2,
			Object p3, Object p4) throws XMLRPCException {
		Object[] params = { p0, p1, p2, p3, p4, };
		return callXMLRPC(method, params);
	}
	[Tags]/**
	 [Tags]* Convenience method call with six parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param p0
	 [Tags]*            method's 1st parameter
	 [Tags]* @param p1
	 [Tags]*            method's 2nd parameter
	 [Tags]* @param p2
	 [Tags]*            method's 3rd parameter
	 [Tags]* @param p3
	 [Tags]*            method's 4th parameter
	 [Tags]* @param p4
	 [Tags]*            method's 5th parameter
	 [Tags]* @param p5
	 [Tags]*            method's 6th parameter
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object p0, Object p1, Object p2,
			Object p3, Object p4, Object p5) throws XMLRPCException {
		Object[] params = { p0, p1, p2, p3, p4, p5, };
		return callXMLRPC(method, params);
	}

	[Tags]/**
	 [Tags]* Convenience method call with seven parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param p0
	 [Tags]*            method's 1st parameter
	 [Tags]* @param p1
	 [Tags]*            method's 2nd parameter
	 [Tags]* @param p2
	 [Tags]*            method's 3rd parameter
	 [Tags]* @param p3
	 [Tags]*            method's 4th parameter
	 [Tags]* @param p4
	 [Tags]*            method's 5th parameter
	 [Tags]* @param p5
	 [Tags]*            method's 6th parameter
	 [Tags]* @param p6
	 [Tags]*            method's 7th parameter
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object p0, Object p1, Object p2,
			Object p3, Object p4, Object p5, Object p6) throws XMLRPCException {
		Object[] params = { p0, p1, p2, p3, p4, p5, p6, };
		return callXMLRPC(method, params);
	}
	[Tags]/**
	 [Tags]* Convenience method call with eight parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param p0
	 [Tags]*            method's 1st parameter
	 [Tags]* @param p1
	 [Tags]*            method's 2nd parameter
	 [Tags]* @param p2
	 [Tags]*            method's 3rd parameter
	 [Tags]* @param p3
	 [Tags]*            method's 4th parameter
	 [Tags]* @param p4
	 [Tags]*            method's 5th parameter
	 [Tags]* @param p5
	 [Tags]*            method's 6th parameter
	 [Tags]* @param p6
	 [Tags]*            method's 7th parameter
	 [Tags]* @param p7
	 [Tags]*            method's 8th parameter
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	public Object call(String method, Object p0, Object p1, Object p2,
			Object p3, Object p4, Object p5, Object p6, Object p7)
			throws XMLRPCException {
		Object[] params = { p0, p1, p2, p3, p4, p5, p6, p7, };
		return callXMLRPC(method, params);
	}
	[Tags]/**
	 [Tags]* Call method with optional parameters
	 [Tags]* @param method
	 [Tags]*            name of method to call
	 [Tags]* @param params
	 [Tags]*            parameters to pass to method (may be null if method has no
	 [Tags]*            parameters)
	 [Tags]* @return deserialized method return value
	 [Tags]* @throws XMLRPCException
	 [Tags]*/
	private Object callXMLRPC(String method, Object[] params)
			throws XMLRPCException {
		try {
			// prepare POST body
			StringWriter bodyWriter = new StringWriter();
			serializer.setOutput(bodyWriter);
			serializer.startDocument(null, null);
			serializer.startTag(null, TAG_METHOD_CALL);
			// set method name
			serializer.startTag(null, TAG_METHOD_NAME).text(method)
					.endTag(null, TAG_METHOD_NAME);
			if (params != null && params.length != 0) {
				// set method params
				serializer.startTag(null, TAG_PARAMS);
				for (int i = 0; i < params.length; i++) {
					serializer.startTag(null, TAG_PARAM).startTag(null,
							XMLRPCSerializer.TAG_VALUE);
					XMLRPCSerializer.serialize(serializer, params[i]);
					serializer.endTag(null, XMLRPCSerializer.TAG_VALUE).endTag(
							null, TAG_PARAM);
				}
				serializer.endTag(null, TAG_PARAMS);
			}
			serializer.endTag(null, TAG_METHOD_CALL);
			serializer.endDocument();
			// set POST body
			HttpEntity entity = new StringEntity(bodyWriter.toString());
			postMethod.setEntity(entity);
			// execute HTTP POST request
			HttpResponse response = client.execute(postMethod);
			// check status code
			int statusCode = response.getStatusLine().getStatusCode();
			if (statusCode != HttpStatus.SC_OK) {
				throw new XMLRPCException("HTTP status code: " + statusCode
						+ " != " + HttpStatus.SC_OK);
			}
			// parse response stuff
			//
			// setup pull parser
			XmlPullParser pullParser = XmlPullParserFactory.newInstance()
					.newPullParser();
			entity = response.getEntity();
			Reader reader = new InputStreamReader(entity.getContent());
			pullParser.setInput(reader);

			// lets start pulling...
			pullParser.nextTag();
			pullParser.require(XmlPullParser.START_TAG, null, TAG_METHOD_RESPONSE);
			pullParser.nextTag(); // either TAG_PARAMS (<params>) or TAG_FAULT
									// (<fault>)
			String tag = pullParser.getName();
			if (tag.equals(TAG_PARAMS)) {
				// normal response
				pullParser.nextTag(); // TAG_PARAM (<param>)
				pullParser.require(XmlPullParser.START_TAG, null, TAG_PARAM);
				pullParser.nextTag(); // TAG_VALUE (<value>)
				// no parser.require() here since its called in
				// XMLRPCSerializer.deserialize() below
				// deserialize result
				Object obj = XMLRPCSerializer.deserialize(pullParser);
				entity.consumeContent();
				return obj;
			} else if (tag.equals(TAG_FAULT)) {
				// fault response
				pullParser.nextTag(); // TAG_VALUE (<value>)
				// no parser.require() here since its called in
				// XMLRPCSerializer.deserialize() below
				// deserialize fault result
				Map<String, Object> map = (Map<String, Object>) XMLRPCSerializer
						.deserialize(pullParser);
				String faultString = (String) map.get(TAG_FAULT_STRING);
				int faultCode = (Integer) map.get(TAG_FAULT_CODE);
				entity.consumeContent();
				throw new XMLRPCFault(faultString, faultCode);
			} else {
				entity.consumeContent();
				throw new XMLRPCException("Bad tag <" + tag
						+ "> in XMLRPC response - neither <params> nor <fault>");
			}
		} catch (XMLRPCException e) {
			// catch & propagate XMLRPCException/XMLRPCFault
			throw e;
		} catch (Exception e) {
			// wrap any other Exception(s) around XMLRPCException
			throw new XMLRPCException(e);
		}
	}
}
```