3. Activity显示方式
```  
public class SwipeTestActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
		SlipView view;
		// Code
		Bitmap backImage = ((BitmapDrawable) getResources().getDrawable(
				R.drawable.back5)).getBitmap();
		Bitmap frotImage = ((BitmapDrawable) getResources().getDrawable(
				R.drawable.frot1)).getBitmap();
		SlipEntity entity = new SlipEntity(backImage, frotImage);
		entity.setCurDockPos(SlipEntity.DOCK_R);
		entity.addDockPos((float) 0.5);
		view = new SlipView(this, entity);
		// Code
		// view = new SlipView(this);
		// XML
		// view = (SlipView)this.findViewById(R.id.slipView);
		setContentView(view);
		view.setOnSlipListener(new OnSlipListener() {
			@Override
			public void slipLeft(SlipEntity slipEntity, MotionEvent event,
					boolean isSuccess) {
				Log.v("Left", Boolean.toString(isSuccess));
			}
			@Override
			public void slipRight(SlipEntity slipEntity, MotionEvent event,
					boolean isSuccess) {
				Log.v("Right", Boolean.toString(isSuccess));
			}
			@Override
			public void slipDock(SlipEntity slipEntity, MotionEvent event,
					float dockPer) {
				Log.v("Dock", "dockPer:" + dockPer);
			}
		});
	}
}
```