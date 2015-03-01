问题1：AsyncTask是多线程吗？
答：是。
问题2：AsyncTask与Handler相比，谁更轻量级？
答：通过看源码，发现AsyncTask实际上就是一个线程池，而网上的说法是AsyncTask比handler要轻量级，显然上不准确的，只能这样说，AsyncTask在代码上比handler要轻量级别，而实际上要比handler更耗资源，因为AsyncTask底层是一个线程池！而Handler仅仅就是发送了一个消息队列，连线程都没有开。
但是，如果异步任务的数据特别庞大，AsyncTask这种线程池结构的优势就体现出来了。
AsyncTask方法：
必选方法：
1，doinbackground(params…) 后台执行，比较耗时的操作都可以放在这里。
注意这里不能直接操作UI。此方法在后台线程执行，完成任务的主要工作，通常需要较长的时间。在执行过程中可以调用
Public progress(progress…)来更新任务的进度。
2，onpostexecute(result)相当于handler处理UI的方式，在这里可以使用在doinbackground得到的结果处理操作UI。此方法在主线程执行，任务执行的结果作为此方法的参数返回。
可选方法：
1，onprogressupdate(progress…) 可以使用进度条增加用户体验度。此方法在主线程执行，用户显示任务执行的进度。
2，onpreExecute()  这里是最新用户调用excute时的接口，当任务执行之前开始调用此方法，可以在这里显示进度对话框。
3，onCancelled()  用户调用取消时，要做的操作。
AsyscTask定义了三种泛型类型params,progress和result.1，params启动任务执行的输入参数，比如http请求的URL2，progress后台任务执行的百分比3，result后台执行任务最终返回的结果，比如String，比如我需要得到的list。
使用AsyncTask类，遵守的准则：
1，Task的实例必须在UI thread中创建。
2，Execute方法必须在UI thread中调用。
3，不要手动的调用onPfreexecute()，onPostExecute(result)Doinbackground(params…),onProgressupdate(progress…)这几个方法。
4，该task只能被执行一次，否则多次调用时将会出现异常。
AsyncTask的整个调用过程都是从execute方法开始的，一旦在主线程中调用execute方法，就可以通过onpreExecute方法，这是一个预处理方法，比如可以在这里开始一个进度框，同样也可以通过onprogressupdate方法给用户一个进度条的显示，增加用户体验；最后通过onpostexecute方法，相当于handler处理UI的方式，在这里可以使用在doinbackground得到的结果处理操作UI。此方法在主线程执行，任务执行的结果作为此方法的参数返回。