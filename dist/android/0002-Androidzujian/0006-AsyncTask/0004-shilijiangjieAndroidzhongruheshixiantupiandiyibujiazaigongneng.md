Android开发当中，经常会碰到图片的异步加载问题（也叫延时加载，英文叫Lazyload）。图片的读取工作是个比较耗时的工作，如果还是从互联网读取图片资源就更加耗时。如果在主线程里处理的时间过长，就会引发著名的应用程序无响应的系统提示（ANR：Application Not Responding）。
本文通过一个名为Demo4FileManager的项目实例来讲解如何实现图片的异步加载功能。该应用的主要功能是列出SD卡下的所有目录和图片文件，用户也可以在此之上修改为读取网络图片的功能。异步加载的实现是通过AsyncTask类来实现的，首先通过一个叫FileLoadTask的类来加载当前目录下的目录或图片文件，在加载完目录或文件数据后，再去执行一个名为ImageLoadTask的类把当前目录下的图片资源解码并通知UI更新这些图片。下面我们一步一步来看看是怎么实现的。
1. 新建一个名为 FileItem.java 的类，封装了基本的目录、文件信息，并且实现Comparable接口，用于目录文件的排序。
```  
public class FileItem implements Comparable<FileItem>{ 
    private String name;
    private String path;
    private int fileType;  //  0：文件，1：目录，2：上级目录
    private Bitmap image;
     // 下面是get和set方法
    .......
    // 和其他文件比较
    public int compareTo(FileItem another) {
        return this.name.compareTo(another.getName()); 
    }
}
```
2. 新建一个名为 FileListActivity.java 的类。在该类里实现一个名为listFile的方法，功能就是列出指定目录下的文件。我们可以看到在检查SD卡的状态为插入后，就启动一个文件加载任务。
```  
private void listFile(String path){
	Log.i(TAG, "listFile()");
	Log.d(TAG, "path:" + path);
	//  SD卡状态
	String status = Environment.getExternalStorageState();
	Log.d(TAG, "status of sdcard:" + status);
	if (status.equals(Environment.MEDIA_MOUNTED)) {//mounted
		task = new FileLoadTask(this, adapter);
		task.execute(path);
	}
}
```
在onCreate方法里，加入listFile那个方法，缺省路径为 /sdcard。FileListAdapter为adapter类，这里就不详细介绍了，在附件里有。
```  
private FileLoadTask task;
@Override
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.file_list);
	adapter = new FileListAdapter(this);
	fileList = (ListView) findViewById(R.id.fileList);
	fileList.setOnItemClickListener(this); //  点击item事件
	fileList.setAdapter(adapter);
	listFile(FileItem.ROOT_PATH);
}
```
3. 新建一个名为 FileLoadTask.java 的类，用来异步加载指定目录下的文件或图片。
```  
public class FileLoadTask extends AsyncTask<String, FileItem, Void> {
	private static final String TAG = FileLoadTask.class.getSimpleName();
	private final static FolderFilter folderFilter = new FolderFilter();
	private final static ImageFilter imageFilter = new ImageFilter();
	private Context context;
	private FileListAdapter adapter;
	private Bitmap folderIcon;
	private Bitmap fileIcon;
	private ImageLoadTask task;
	// 初始化
	public FileLoadTask(Context context, FileListAdapter adapter) {
		Log.i(TAG, "FileLoadTask()");

		this.context = context;
		this.adapter = adapter;
		folderIcon = BitmapFactory.decodeResource(context.getResources(),
				R.drawable.folder);
		fileIcon = BitmapFactory.decodeResource(context.getResources(),
				R.drawable.file);
	}
	@Override
	protected void onPreExecute() {
		Log.i(TAG, "onPreExecute()");
		// 在执行任务前，先把adapter里的数据清空
		adapter.clear();
	}
	@Override
	protected Void doInBackground(String... path) {
		Log.i(TAG, "doInBackground()");
		File parent = new File(path[0]);
		if (parent.isDirectory()) {
			// 设置返回按钮
			if (!path[0].equals(FileItem.ROOT_PATH)) {
				FileItem root = new FileItem();
				root.setName(FileItem.ROOT_NAME);
				root.setFileType(FileItem.FILE_ROOT);
				publishProgress(root);
			}
			// 获取当前目录下的子目录
			File[] files = parent.listFiles(folderFilter);
			Arrays.sort(files);
			for (int i = 0; i < files.length; i++) {
				if (isCancelled())
					return null;

				File file = files;
				FileItem bean = new FileItem();
				bean.setName(file.getName());
				bean.setPath(file.getAbsolutePath());
				bean.setFileType(FileItem.FILE_DIR);
				publishProgress(bean); // 处理好一个目录，通知任务去更新UI
			}
			// 获取当前目录下的图片文件
			files = parent.listFiles(imageFilter);
			Arrays.sort(files);
			for (int i = 0; i < files.length; i++) {
				if (isCancelled())
					return null;

				File file = files;
				FileItem bean = new FileItem();
				bean.setName(file.getName());
				bean.setPath(file.getAbsolutePath());
				bean.setFileType(FileItem.FILE_IMAGE);
				publishProgress(bean); // 处理好一个文件，通知任务去更新UI
			}
		}
		return null;
	}
	@Override
	public void onProgressUpdate(FileItem... files) {
		Log.i(TAG, "onProgressUpdate()");
		if (isCancelled())
			return;

		// 收到要处理的类，根据目录或文件先设置缺省的图片
		FileItem bean = files[0];
		if (bean.getFileType() == FileItem.FILE_IMAGE) {
			bean.setImage(fileIcon);
		} else {
			bean.setImage(folderIcon);
		}
		// 更新数据，列表会显示对应数据
		adapter.add(bean);
		adapter.notifyDataSetChanged();
	}
	@Override
	protected void onPostExecute(Void result) {
		Log.i(TAG, "onPostExecute()");

		// 启动图片加载任务
		if (task != null && task.getStatus() == AsyncTask.Status.RUNNING) {
			task.cancel(true);
		}
		task = new ImageLoadTask(context, adapter);
		task.execute();
	}
}
```
4. 新建一个名为 ImageLoadTask.java 的类，用来加载图片资源。
```  
public class ImageLoadTask extends AsyncTask<Void, Void, Void> {
	private static final String TAG = ImageLoadTask.class.getSimpleName();
	private static int thumbnailWidth = 70;
	private static int thumbnailHeight = 100;
	private FileListAdapter adapter;
	// 初始化
	public ImageLoadTask(Context context, FileListAdapter adapter) {
		Log.i(TAG, "ImageLoadTask()");
		this.adapter = adapter;
	}
	@Override
	protected Void doInBackground(Void... voids) {
		Log.i(TAG, "doInBackground()");
		BitmapFactory.Options ptions = new BitmapFactory.Options();
		options.inSampleSize = 16;
		// 要处理的文件
		for (int i = 0; i < adapter.getCount(); i++) {
			FileItem bean = adapter.getItem(i);
			if (bean.getFileType() == FileItem.FILE_DIR) {
				continue; // 非图片文件
			}
			try {
				// 生成缩略图
				options.inJustDecodeBounds = true;
				options.outWidth = 0;
				options.outHeight = 0;
				options.inSampleSize = 1;
				BitmapFactory.decodeFile(bean.getPath(), options);
				if (options.outWidth > 0 && options.outHeight > 0) {
					// Now see how much we need to scale it down.
					int widthFactor = (options.outWidth + thumbnailWidth - 1)
							/ thumbnailWidth;
					int heightFactor = (options.outHeight + thumbnailHeight - 1)
							/ thumbnailHeight;
					widthFactor = Math.max(widthFactor, heightFactor);
					widthFactor = Math.max(widthFactor, 1);

					// Now turn it into a power of two.
					if (widthFactor > 1) {
						if ((widthFactor & (widthFactor - 1)) != 0) {
							while ((widthFactor & (widthFactor - 1)) != 0) {
								widthFactor &= widthFactor - 1;
							}
							widthFactor <<= 1;
						}
					}
					options.inSampleSize = widthFactor;
					options.inJustDecodeBounds = false;
					Bitmap bitmap = BitmapFactory.decodeFile(bean.getPath(),
							options);
					if (bitmap != null) {
						bean.setImage(bitmap); // 读取到图片资源，加入到FileItem对象中
						publishProgress(); // 通知去更新UI
					}
				}
			} catch (Exception e) {
				// That's okay, guess it just wasn't a bitmap.
			}
		}
		return null;
	}
	@Override
	public void onProgressUpdate(Void... voids) {
		Log.i(TAG, "onProgressUpdate()");
		if (isCancelled())
			return;
		// 更新UI
		adapter.notifyDataSetChanged();
	}
}
```