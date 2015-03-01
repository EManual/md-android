Android平台上可以左右滑动的控件，总共3个文件，其中一个用于是Activity
![img](P)  
![img](P)  
包括三个类：
1. SlipView用于显示
```  
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.drawable.BitmapDrawable;
import android.util.AttributeSet;
import android.util.Log;
import android.view.MotionEvent;
import android.view.View;
 /**
  * Slip the front part to some dock position
  */
public class SlipView extends View {
	private static final String TAG = "SlipView";
	 /** Listen slip on the block, no matter the event is success or fail */
	private OnSlipListener onSlipListener;
	 /** Slip entity to set the value about backImage, frontImage position */
	private SlipEntity slipEntity;
	private float tmpTouchX;
	private float tmpTouchGap;
	public SlipView(Context context) {
		super(context);
		Bitmap backImage = ((BitmapDrawable) getResources().getDrawable(
				R.drawable.back5)).getBitmap();
		Bitmap frotImage = ((BitmapDrawable) getResources().getDrawable(
				R.drawable.frot1)).getBitmap();
		this.slipEntity = new SlipEntity(backImage, frotImage);
	}
	public SlipView(Context context, AttributeSet attr) {
		super(context, attr);
		Bitmap backImage = ((BitmapDrawable) getResources().getDrawable(
				R.drawable.back5)).getBitmap();
		Bitmap frotImage = ((BitmapDrawable) getResources().getDrawable(
				R.drawable.frot1)).getBitmap();
		this.slipEntity = new SlipEntity(backImage, frotImage);
	}
	public SlipView(Context context, SlipEntity slipEntity) {
		super(context);
		this.slipEntity = slipEntity;
	}
	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		if (!this.slipEntity.isInit) {
			Log.v(TAG, "Init SlipEntity");
			this.slipEntity.initSlipEntity(this.getWidth(), this.getHeight());
		}
		canvas.drawBitmap(this.slipEntity.getBackImage(),
				this.slipEntity.xBackStart, this.slipEntity.yBackStart, null);
		canvas.drawBitmap(this.slipEntity.getFrotImage(),
				this.slipEntity.xFrotStart, this.slipEntity.yFrotStart, null);
	}
	 /**
	  * listen touch events and notify listener
	  */
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		Log.v(TAG, "Touch Position:" + event.getX());
		switch (event.getAction()) {
		case MotionEvent.ACTION_DOWN:
			// Log.v(TAG, "Down");
			if (this.slipEntity.isPointInImage(SlipEntity.FLAG_FROT,
					event.getX(), event.getY())) {
				this.tmpTouchX = event.getX();
				this.tmpTouchGap = event.getX() - this.slipEntity.xFrotStart;
			}
			break;
		case MotionEvent.ACTION_MOVE:
			// Log.v(TAG, "Move");
			this.slipEntity.isReachRight();
			this.slipEntity.isReachLeft();
			// if point(x,y) is not in back image, front image will not move
			if (this.slipEntity.isPointInImage(SlipEntity.FLAG_BACK,
					event.getX(), event.getY())) {
				// Log.v(TAG, "Move2");
				this.slipEntity.xFrotStart = event.getX() - this.tmpTouchGap;
			}
			break;
		case MotionEvent.ACTION_UP:
			Log.v(TAG, "Up");
			int flagLR = 0;
			if (event.getX() < this.tmpTouchX)
				flagLR = 1;
			else
				flagLR = 2;
			float dockPer = this.slipEntity.isDock();
			if (dockPer != -1) {
				// Dock at some Position
				if (this.onSlipListener != null)
					this.onSlipListener.slipDock(slipEntity, event,
							this.slipEntity.getCurDockPer());
				if (event.getX() < this.tmpTouchX) {
					// Slip: <==
					if (dockPer == 0.0) {
						// Reached
						if (this.onSlipListener != null)
							this.onSlipListener.slipLeft(slipEntity, event,
									true);
					} else {
						// Not Reached
						this.slipEntity.xFrotStart = this.slipEntity
								.getCurDockPos();
						if (this.onSlipListener != null)
							this.onSlipListener.slipLeft(slipEntity, event,
									false);
					}
				} else {
					// Slip: ==>
					if (dockPer == 1.0) {
						// Reached
						if (this.onSlipListener != null)
							this.onSlipListener.slipRight(slipEntity, event,
									true);
					} else {
						// Not Reached
						this.slipEntity.xFrotStart = this.slipEntity
								.getCurDockPos();
						if (this.onSlipListener != null)
							this.onSlipListener.slipRight(slipEntity, event,
									false);
					}
					break;
				}
			} else {
				// No dock
				this.slipEntity.xFrotStart = this.slipEntity.getCurDockPos();
				if (flagLR == 1)
					this.onSlipListener.slipLeft(slipEntity, event, false);
				else
					this.onSlipListener.slipRight(slipEntity, event, false);
			}
			// if (event.getX() < this.tmpTouchX)
			// {
			// // Slip: <== // if (this.slipEntity.isReachLeft())
			// { // // Reached
			// if (this.onSlipListener != null)
			// this.onSlipListener.slipLeft(slipEntity, event, true);
			// }
			// else
			// {
			// // Not Reached
			// this.slipEntity.xFrotStart = this.slipEntity.getCurDockPos();
			// if (this.onSlipListener != null)
			// this.onSlipListener.slipLeft(slipEntity, event, false);
			// }
			// }
			// else
			// {
			// // Slip: ==>
			// if (this.slipEntity.isReachRight())
			// {
			// // Reached
			// if (this.onSlipListener != null)
			// this.onSlipListener.slipRight(slipEntity, event, true);
			// }
			// else
			// {
			// // Not Reached
			// this.slipEntity.xFrotStart = this.slipEntity.getCurDockPos();
			// if (this.onSlipListener != null)
			// this.onSlipListener.slipRight(slipEntity, event, false);
			// }
			// break;
			// }
		}
		this.invalidate();
		return true;
	}
	 /**
	  * Listener on slip event on slippery view author diydyq
	  */
	public interface OnSlipListener {
		 /**
		  * Listen a slip after touch down and up, not including touch move
		  * @param slipEntity
		  * @param event
		  * @param isSuccess
		  */
		public abstract void slipLeft(SlipEntity slipEntity, MotionEvent event,
				boolean isSuccess);
		 /**
		  * Listen a slip after touch down and up, not including touch move
		  * @param slipEntity
		  * @param event
		  * @param isSuccess
		  */
		public abstract void slipRight(SlipEntity slipEntity,
				MotionEvent event, boolean isSuccess);
		 /**
		  * Listen some dock(more than DOCK_L,DOCK_R), normally need not
		  * implement it unless more docks are needed.
		  * @param slipEntity
		  * @param event
		  * @param dockPer
		  */
		public abstract void slipDock(SlipEntity slipEntity, MotionEvent event,
				float dockPer);
	}
	public OnSlipListener getOnSlipListener() {
		return onSlipListener;
	}
	public void setOnSlipListener(OnSlipListener onSlipListener) {
		this.onSlipListener = onSlipListener;
	}
}
```