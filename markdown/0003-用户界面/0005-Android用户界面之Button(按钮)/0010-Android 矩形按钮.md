我们今天就给大家分享一个实例，下面就是矩形button实例，我们就来看看吧。
```  
//动态创建一个按钮
_hwndArrowButton = CreateWindow("BUTTON",//窗口类型，为按钮
		NULL,
		WS_VISIBLE |WS_POPUP | BS_OWNERDRAW,//按钮样式
		428,66,50,50,//按钮位置和大小
		_hWnd,//父窗口句柄
		(HMENU)ID_ARROW_UP,//按钮的资源ID
		g_hInstance,NULL);
//设置此按钮窗口的区域
SetupRegion(_hwndArrowButton , IDB_ARROW, RGB(255,255,255) );
//设置区域函数实现如下（来源于网络，有修改）
void SetupRegion(HWND hWnd, int buttonId, COLORREF TransColor)
{
	HDC hDC;
	hDC = GetWindowDC(hWnd);
	//创建与传入DC兼容的临时DC
	HDC memDC= ::CreateCompatibleDC(hDC);
	HBITMAP hOldMemBmp = LoadBitmap(g_hInstance, MAKEINTRESOURCE(buttonId));
	//将位图选入临时DC
	SelectObject(memDC,hOldMemBmp);
	//创建总的窗体区域，初始region为0
	HRGN wndRgn = ::CreateRectRgn(0,0,0,0);
	//取得位图参数，这里要用到位图的长和宽 
	BITMAP bit;
	GetObject(hOldMemBmp,sizeof(BITMAP),&bit);
	int y;
	for(y=0;y<bit.bmHeight ;y++)
	{
		//保存临时region
		HRGN rgnTemp;
		int iX = 0;
		do
		{
			//跳过透明色找到下一个非透明色的点.
			while (iX < bit.bmWidth && GetPixel(memDC,iX, y) == TransColor)
			iX++;
			//记住这个起始点
			int iLeftX = iX;
			//寻找下个透明色的点
			while (iX < bit.bmWidth && GetPixel(memDC,iX, y) != TransColor)
			++iX;
			//创建一个包含起点与终点间高为1像素的临时“region”
			rgnTemp = CreateRectRgn(iLeftX, y, iX, y+1);
			//合并到主"region".
			CombineRgn(wndRgn,wndRgn,rgnTemp, RGN_OR);
			//删除临时"region",否则下次创建时和出错
			DeleteObject(rgnTemp);
		} while (iX <bit.bmWidth );
			iX = 0;
	}
	if(hOldMemBmp)
		SelectObject(memDC,hOldMemBmp);
	if(SetWindowRgn(hWnd,wndRgn,TRUE)==0)
	{
		return;
	}
	SetForegroundWindow(hWnd);
} 
```