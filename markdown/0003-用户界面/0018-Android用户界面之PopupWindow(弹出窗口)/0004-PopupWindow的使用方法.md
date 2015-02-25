代码如下：
```  
public class popWindow extends Activity {
	Button button_show;
	View contentView;
	PopupWindow pWindow;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		button_show = (Button) findViewById(R.id.show_popwindow);
		contentView = View.inflate(popWindow.this, R.layout.pwcontent, null);
		contentView.setBackgroundDrawable(getResources().getDrawable(
				R.drawable.fantasy_pictuer_06));
		contentView.setOnClickListener(new View.OnClickListener() {
			public void onClick(View v) {
				pWindow.dismiss();
			}
		});
		pWindow = new PopupWindow(contentView);
		pWindow.setWindowLayoutMode(ViewGroup.LayoutParams.WRAP_CONTENT,
				ViewGroup.LayoutParams.WRAP_CONTENT);
		button_show.setOnClickListener(new View.OnClickListener() {
			public void onClick(View v) {
				try {
					if (pWindow.isShowing()) {
						pWindow.dismiss();
					}
					pWindow.showAsDropDown(v);
					pWindow.setFocusable(true);
				} catch (Exception e) {
					Toast.makeText(popWindow.this, e.getMessage(),
							Toast.LENGTH_SHORT);
				}
			}
		});
	}
}
```