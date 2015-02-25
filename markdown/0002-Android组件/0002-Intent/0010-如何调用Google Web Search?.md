android提供了一个很方便的调用方式，就是用Intent去调用系统的Activity，代码如下：
```  
//搜索
Intent search = new Intent(Intent.ACTION_WEB_SEARCH);
search.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
search.putExtra(SearchManager.QUERY, "Android");
final Bundle appData = getIntent().getBundleExtra(SearchManager.APP_DATA);
if (appData != null) {
    search.putExtra(SearchManager.APP_DATA, appData);
}
startActivity(search);
```
执行这段代码之后，就会跳转到google的网站并自动搜索与Android相关的记录。