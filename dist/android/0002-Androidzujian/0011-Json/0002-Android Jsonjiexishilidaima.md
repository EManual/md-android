来自Google官方的有关Android平台的JSON解析示例，如果远程服务器使用了json而不是xml的数据提供，在Android平台上已经内置的org.json包可以很方便的实现手机客户端的解析处理。下面一起分析下这个例子，帮助Android开发者需要有关HTTP通讯、正则表达式、JSON解析、appWidget开发的一些知识。
```  
public class WordWidget extends AppWidgetProvider { // appWidget
	@Override
	public void onUpdate(Context context, AppWidgetManager appWidgetManager,
			int[] appWidgetIds) {
		context.startService(new Intent(context, UpdateService.class)); // 避免ANR，所以Widget中开了个服务
	}
	public static class UpdateService extends Service {
		@Override
		public void onStart(Intent intent, int startId) {
			// Build the widget update for today
			RemoteViews updateViews = buildUpdate(this);
			ComponentName thisWidget = new ComponentName(this, WordWidget.class);
			AppWidgetManager manager = AppWidgetManager.getInstance(this);
			manager.updateAppWidget(thisWidget, updateViews);
		}
		public RemoteViews buildUpdate(Context context) {
			// Pick out month names from resources
			Resources res = context.getResources();
			String[] monthNames = res.getStringArray(R.array.month_names);
			Time today = new Time();
			today.setToNow();
			String pageName = res.getString(R.string.template_wotd_title,
					monthNames[today.month], today.monthDay);
			RemoteViews updateViews = null;
			String pageContent = "";
			try {
				SimpleWikiHelper.prepareUserAgent(context);
				pageContent = SimpleWikiHelper.getPageContent(pageName, false);
			} catch (ApiException e) {
				Log.e("WordWidget", "Couldn't contact API", e);
			} catch (ParseException e) {
				Log.e("WordWidget", "Couldn't parse API response", e);
			}
			Pattern pattern = Pattern
					.compile(SimpleWikiHelper.WORD_OF_DAY_REGEX); // 正则表达式处理，有关定义见下面的SimpleWikiHelper类
			Matcher matcher = pattern.matcher(pageContent);
			if (matcher.find()) {
				updateViews = new RemoteViews(context.getPackageName(),
						R.layout.widget_word);
				String wordTitle = matcher.group(1);
				updateViews.setTextViewText(R.id.word_title, wordTitle);
				updateViews.setTextViewText(R.id.word_type, matcher.group(2));
				updateViews.setTextViewText(R.id.definition, matcher.group(3)
						.trim());
				String definePage = res.getString(R.string.template_define_url,
						Uri.encode(wordTitle));
				Intent defineIntent = new Intent(Intent.ACTION_VIEW,
						Uri.parse(definePage)); // 这里是打开相应的网页，所以Uri是http的url，action是view即打开web浏览器
				PendingIntent pendingIntent = PendingIntent.getActivity(
						context, 0 /* no requestCode */, defineIntent, 0 /*
																		  * no
																		  * flags
																		  */);
				updateViews.setOnClickPendingIntent(R.id.widget, pendingIntent); // 单击Widget打开Activity
			} else {
				updateViews = new RemoteViews(context.getPackageName(),
						R.layout.widget_message);
				CharSequence errorMessage = context
						.getText(R.string.widget_error);
				updateViews.setTextViewText(R.id.message, errorMessage);
			}
			return updateViews;
		}
		@Override
		public IBinder onBind(Intent intent) {
			// We don't need to bind to this service
			return null;
		}
	}
}
```
有关网络通讯的实体类，以及一些常量定义如下:
```  
public class SimpleWikiHelper {
	private static final String TAG = "SimpleWikiHelper";
	public static final String WORD_OF_DAY_REGEX = "(?s)\\{\\{wotd\\|(.+?)\\|(.+?)\\|([^\\|]+).*?\\}\\}";
	private static final String WIKTIONARY_PAGE = "http://en.wiktionary.org/w/api.php?action=query&prop=revisions&titles=%s&
			+ "rvprop=content&format=json%s";
	private static final String WIKTIONARY_EXPAND_TEMPLATES = "&rvexpandtemplates=true";
	private static final int HTTP_STATUS_OK = 200;
	private static byte[] sBuffer = new byte[512];
	private static String sUserAgent = null;
	public static class ApiException extends Exception {
		public ApiException(String detailMessage, Throwable throwable) {
			super(detailMessage, throwable);
		}
		public ApiException(String detailMessage) {
			super(detailMessage);
		}
	}
	public static class ParseException extends Exception {
		public ParseException(String detailMessage, Throwable throwable) {
			super(detailMessage, throwable);
		}
	}
	public static void prepareUserAgent(Context context) {
		try {
			// Read package name and version number from manifest
			PackageManager manager = context.getPackageManager();
			PackageInfo info = manager.getPackageInfo(context.getPackageName(),
					0);
			sUserAgent = String.format(
					context.getString(R.string.template_user_agent),
					info.packageName, info.versionName);
		} catch (NameNotFoundException e) {
			Log.e(TAG, "Couldn't find package information in PackageManager", e);
		}
	}
	public static String getPageContent(String title, boolean expandTemplates)
			throws ApiException, ParseException {
		String encodedTitle = Uri.encode(title);
		String expandClause = expandTemplates ? WIKTIONARY_EXPAND_TEMPLATES
				: "";
		String content = getUrlContent(String.format(WIKTIONARY_PAGE,
				encodedTitle, expandClause));
		try {
			JSONObject response = new JSONObject(content);
			JSONObject query = response.getJSONObject("query");
			JSONObject pages = query.getJSONObject("pages");
			JSONObject page = pages.getJSONObject((String) pages.keys().next());
			JSONArray revisions = page.getJSONArray("revisions");
			JSONObject revision = revisions.getJSONObject(0);
			return revision.getString("*");
		} catch (JSONException e) {
			throw new ParseException("Problem parsing API response", e);
		}
	}
	protected static synchronized String getUrlContent(String url)
			throws ApiException {
		if (sUserAgent == null) {
			throw new ApiException("User-Agent string must be prepared");
		}
		HttpClient client = new DefaultHttpClient();
		HttpGet request = new HttpGet(url);
		request.setHeader("User-Agent", sUserAgent); // 设置客户端标识
		try {
			HttpResponse response = client.execute(request);

			StatusLine status = response.getStatusLine();
			if (status.getStatusCode() != HTTP_STATUS_OK) {
				throw new ApiException("Invalid response from server: 
						+ status.toString());
			}
			HttpEntity entity = response.getEntity();
			InputStream inputStream = entity.getContent(); // 获取HTTP返回的数据流
			ByteArrayOutputStream content = new ByteArrayOutputStream();
			int readBytes = 0;
			while ((readBytes = inputStream.read(sBuffer)) != -1) {
				content.write(sBuffer, 0, readBytes); // 转化为字节数组流
			}
			return new String(content.toByteArray()); // 从字节数组构建String
		} catch (IOException e) {
			throw new ApiException("Problem communicating with API", e);
		}
	}
}
```
有关整个每日维基的widget例子比较简单，主要是帮助大家积累常用代码，了解Android平台 JSON的处理方式，毕竟很多Server还是Java的。