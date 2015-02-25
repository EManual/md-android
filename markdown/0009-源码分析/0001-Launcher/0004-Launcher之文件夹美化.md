需要的源代码包：（还需要在Eclipse中配置）
![img](P)  
从launcher源码中很容易变可以看出需要修改的文件，主要修改FolderIcon.java这个文件。修改后的代码如下：
```  
public class FolderIcon extends BubbleTextView implements DropTarget {
	private UserFolderInfo mInfo;
	private Launcher mLauncher;
	private Drawable mCloseIcon;
	private Drawable mOpenIcon;
	// add by hmg for FolderIcon {
	private IconCache mIconCache;
	private static final int ICON_COUNT = 4; // 可显示的缩略图数
	private static final int NUM_COL = 2; // 每行显示的个数
	private static final int PADDING = 1; // 内边距
	private static final int MARGIN = 7; // 外边距
	// add by hmg for FolderIcon }
	public FolderIcon(Context context, AttributeSet attrs) {
		super(context, attrs);
		mIconCache = ((LauncherApplication) mContext.getApplicationContext()).getIconCache();
	}
	public FolderIcon(Context context) {
		super(context);
		mIconCache = ((LauncherApplication) mContext.getApplicationContext()).getIconCache();
	}
	static FolderIcon fromXml(int resId, Launcher launcher, ViewGroup group,
			UserFolderInfo folderInfo) {
				resId, group, false);
		// final Resources resources = launcher.getResources();
		// Drawable d = resources.getDrawable(R.drawable.ic_launcher_folder);
		// icon.mCloseIcon = d;
		// icon.mOpenIcon =
		// resources.getDrawable(R.drawable.ic_launcher_folder_open);
		// icon.setCompoundDrawablesWithIntrinsicBounds(null, d, null, null);
		icon.setText(folderInfo.title);
		icon.setTag(folderInfo);
		icon.setOnClickListener(launcher);
		icon.mInfo = folderInfo;
		icon.mLauncher = launcher;
		icon.updateFolderIcon(); // 更新图标
		folderInfo.setFolderIcon(icon); // 设置FolderIcon
		return icon;
	}
	public void updateFolderIcon() {
		float x, y;
		final Resources resources = mLauncher.getResources();
		Bitmap closebmp = BitmapFactory.decodeResource(resources,
				R.drawable.icon_folder); // 获取FolderIcon关闭时的背景图
		Bitmap openbmp = BitmapFactory.decodeResource(resources,
				R.drawable.icon_folder_open); // 获取FolderIcon打开时的背景图
		int iconWidth = closebmp.getWidth(); // icon的宽度
		int iconHeight = closebmp.getHeight();
		Bitmap folderclose = Bitmap.createBitmap(iconWidth, iconHeight,
				Bitmap.Config.ARGB_8888);
		Bitmap folderopen = Bitmap.createBitmap(iconWidth, iconHeight,
				Bitmap.Config.ARGB_8888);
		Canvas canvas = new Canvas(folderclose);
		canvas.drawBitmap(closebmp, 0, 0, null); // 绘制背景
		Matrix matrix = new Matrix(); // 创建操作图片用的Matrix对象
		float scaleWidth = (iconWidth - MARGIN * 2) / NUM_COL - 2 * PADDING; // 计算缩略图的宽(高与宽相同)
		float scale = (scaleWidth / iconWidth); // 计算缩放比例
		matrix.postScale(scale, scale); // 设置缩放比例
		for (int i = 0; i < ICON_COUNT; i++) {
			if (i < mInfo.contents.size()) {
				x = MARGIN + PADDING * (2 * (i % NUM_COL) + 1) + scaleWidth
						[Tags]* (i % NUM_COL);
				y = MARGIN + PADDING * (2 * (i / NUM_COL) + 1) + scaleWidth
						[Tags]* (i / NUM_COL);
				ShortcutInfo scInfo = (ShortcutInfo) mInfo.contents.get(i);
				Bitmap iconbmp = scInfo.getIcon(mIconCache); // 获取缩略图标
				Bitmap scalebmp = Bitmap.createBitmap(iconbmp, 0, 0, iconWidth,
						iconHeight, matrix, true);
				canvas.drawBitmap(scalebmp, x, y, null);
			}
		}
		mCloseIcon = new FastBitmapDrawable(folderclose); // 将bitmap转换为Drawable
		setCompoundDrawablesWithIntrinsicBounds(null, mCloseIcon, null, null);
		canvas = new Canvas(folderopen);
		canvas.drawBitmap(folderclose, 0, 0, null);
		canvas.drawBitmap(openbmp, 0, 0, null);
		mOpenIcon = new FastBitmapDrawable(folderopen); // 绘制open图片
	}
	public boolean acceptDrop(DragSource source, int x, int y, int xOffset,
			int yOffset, DragView dragView, Object dragInfo) {
		final ItemInfo item = (ItemInfo) dragInfo;
		final int itemType = item.itemType;
		return (itemType == LauncherSettings.Favorites.ITEM_TYPE_APPLICATION || itemType == LauncherSettings.Favorites.ITEM_TYPE_SHORTCUT)
				&& item.container != mInfo.id;
	}
	public Rect estimateDropLocation(DragSource source, int x, int y,
			int xOffset, int yOffset, DragView dragView, Object dragInfo,
			Rect recycle) {
		return null;
	}
	public void onDrop(DragSource source, int x, int y, int xOffset,
			int yOffset, DragView dragView, Object dragInfo) {
		ShortcutInfo item;
		if (dragInfo instanceof ApplicationInfo) {
			// Came from all apps -- make a copy
			item = ((ApplicationInfo) dragInfo).makeShortcut();
		} else {
			item = (ShortcutInfo) dragInfo;
		}
		mInfo.add(item);
		LauncherModel.addOrMoveItemInDatabase(mLauncher, item, mInfo.id, 0, 0,
				0);
		updateFolderIcon(); // 拖拽放入时更新
	}
	public void onDragEnter(DragSource source, int x, int y, int xOffset,
			int yOffset, DragView dragView, Object dragInfo) {
		setCompoundDrawablesWithIntrinsicBounds(null, mOpenIcon, null, null);
	}
	public void onDragOver(DragSource source, int x, int y, int xOffset,
			int yOffset, DragView dragView, Object dragInfo) {
	}
	public void onDragExit(DragSource source, int x, int y, int xOffset,
			int yOffset, DragView dragView, Object dragInfo) {
		setCompoundDrawablesWithIntrinsicBounds(null, mCloseIcon, null, null);
	}
}
```
将文件拖拽进入文件夹时响应FolderIcon中的onDrop,所以添加updateFolderIcon();
以上代码可以实现将图标拖拽进文件夹时实时更新缩略图显示，还没有对拖拽出文件夹时更新显示，所以还需要修改其他地方。跟踪代码可以看出拖拽离开文件夹时响应UserFolder中方法onDropCompleted，需要修改UserFolder.java：
```  
public void onDropCompleted(View target, boolean success) {
	if (success) {
		ShortcutsAdapter adapter = (ShortcutsAdapter) mContent.getAdapter();
		adapter.remove(mDragItem);
		// 拖拽离开时更新
		((UserFolderInfo) mInfo).mFolderIcon.updateFolderIcon();
	}
}
public void onDrop(DragSource source, int x, int y, int xOffset,
		int yOffset, DragView dragView, Object dragInfo) {
	ShortcutInfo item;
	if (dragInfo instanceof ApplicationInfo) {
		// Came from all apps -- make a copy
		item = ((ApplicationInfo) dragInfo).makeShortcut();
	} else {
		item = (ShortcutInfo) dragInfo;
	}
	((ShortcutsAdapter) mContent.getAdapter()).add(item);
	LauncherModel.addOrMoveItemInDatabase(mLauncher, item, mInfo.id, 0, 0, 0);
	// 将文件直接拖拽到打开的文件夹更新
	((UserFolderInfo) mInfo).mFolderIcon.updateFolderIcon();
}
```
从以上代码可以看出为了传递FolderIcon对象，所以我们还需要为UserFolderInfo添加一个mFolderIcon成员，修改UserFolderInfo.java:
```  
protected FolderIcon mFolderIcon = null; 
void setFolderIcon(FolderIcon icon) {
	mFolderIcon = icon;
}
```
以上代码是在android2.2, 480*320下测试的，其他分辨率的可以修改
```  
private static final int ICON_COUNT = 4; // 可显示的缩略图数
private static final int NUM_COL = 2; // 每行显示的个数
private static final int PADDING = 1; // 内边距
private static final int MARGIN = 7; // 外边距
```
的值。