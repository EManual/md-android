工程中需要做https访问，于是乎就研究了一番。
1. 解决证书（SSL）问题，
```  
public class EasySSLSocketFactory extends SSLSocketFactory {
	// SSLContext sslContext = SSLContext.getInstance("TLS");
	SSLContext sslContext = SSLContext.getInstance("SSL");
	public EasySSLSocketFactory(KeyStore truststore)
			throws NoSuchAlgorithmException, KeyManagementException,
			KeyStoreException, UnrecoverableKeyException {
		super(truststore);
		TrustManager tm = new X509TrustManager() {
			public void checkClientTrusted(X509Certificate[] chain,
					String authType) throws CertificateException {
			}

			public void checkServerTrusted(X509Certificate[] chain,
					String authType) throws CertificateException {
			}
			public X509Certificate[] getAcceptedIssuers() {
				return null;
			}
		};
		sslContext.init(null, new TrustManager[] { tm }, null);
	}
	@Override
	public Socket createSocket(Socket socket, String host, int port,
			boolean autoClose) throws IOException, UnknownHostException {
		return sslContext.getSocketFactory().createSocket(socket, host, port,
				autoClose);
	}
	@Override
	public Socket createSocket() throws IOException {
		return sslContext.getSocketFactory().createSocket();
	}
}
```
2. 移动网络的参数设置。
```  
WifiManager wifiManager = (WifiManager) context.getSystemService(Context.WIFI_SERVICE);
if (!wifiManager.isWifiEnabled()) {
	// 获取当前正在使用的APN接入点
	Uri uri = Uri.parse("content://telephony/carriers/preferapn");
	Cursor mCursor = context.getContentResolver().query(uri, null, null, null, null);
	if (mCursor != null && mCursor.moveToFirst()) {
		// 游标移至第一条记录，当然也只有一条
		String proxyStr = mCursor.getString(mCursor.getColumnIndex("proxy"));
		if (proxyStr != null && proxyStr.trim().length() > 0) {
			HttpHost proxy = new HttpHost(proxyStr, 80);
			client.getParams().setParameter(ConnRouteParams.DEFAULT_PROXY, proxy);
		}
		mCursor.close();
	}
}
```