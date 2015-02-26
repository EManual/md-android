2. SlipEntity用于调整前景、背景图片位置等的操作。 
```  
import java.util.HashMap;
import java.util.Map;
import android.graphics.Bitmap;
import android.util.Log;
 /**
  * Slip entity used to set position and maybe boundary of back and front image
  */
public class SlipEntity {
	public static final int FLAG_BACK = 1;
	public static final int FLAG_FROT = 2;
	public static final float DOCK_L = 0;
	public static final float DOCK_M = (float) 0.5;
	public static final float DOCK_R = 1;
	public static final float MICRO_X = 10;
	 /** Background image */
	Bitmap backImage;
	 /** Front image */
	Bitmap frotImage;
	 /** Start Position of back image */
	float xBackStart;
	 /** Start Position of back image */
	float yBackStart;
	 /** Start Position of front image */
	float xFrotStart;
	 /** Start Position of front image */
	float yFrotStart;
	 /** initial Position of front image */
	float xFrotInitPos;
	 /** initial Position of front image */
	float yFrotInitPos;
	 /** Margin of front and back image in X-Axis */
	float xMarginLeft;
	 /** Margin of front and back image in Y-Axis */
	float yMarginTop;
	 /** Containing dock position of the front image */
	Map<Float, Float> dockPosList = new HashMap<Float, Float>();
	 /** Current dock Percentage: DOCK_L | DOCK_M | DOCK_R */
	float curDockPer = DOCK_L;
	 /** Weather has invoked initSlipEntity() */
	boolean isInit = false;
	public SlipEntity() {
	}
	public SlipEntity(Bitmap backImage, Bitmap frotImage) {
		this.backImage = backImage;
		this.frotImage = frotImage;
	}
	public SlipEntity(float xBackStart, float yBackStart, float xFrotStart,
			float yFrotStart) {
		this.xBackStart = xBackStart;
		this.yBackStart = yBackStart;
		this.xFrotStart = xFrotStart;
		this.yFrotStart = yFrotStart;
	}
	public void initSlipEntity(float viewWidth, float viewHeight) {
		this.xBackStart = (viewWidth - this.backImage.getWidth()) / 2;
		this.yBackStart = (viewHeight - this.backImage.getHeight()) / 2;
		this.xMarginLeft = 5;
		this.yMarginTop = (this.backImage.getHeight() - this.frotImage
				.getHeight()) / 2;
		this.xFrotInitPos = this.xBackStart + this.xMarginLeft;
		this.yFrotInitPos = this.yBackStart + this.yMarginTop;
		this.xFrotStart = this.xFrotInitPos;
		this.yFrotStart = this.yFrotInitPos;
		// Add dock position
		float dockL = this.xFrotInitPos;
		float dockR = this.xBackStart + this.backImage.getWidth()
				- this.frotImage.getWidth() - this.xMarginLeft;
		this.dockPosList.put(DOCK_L, dockL);
		this.dockPosList.put(DOCK_R, dockR);
		for (Float dockPer : this.dockPosList.keySet()) {
			if (this.dockPosList.get(dockPer) == 0) {
				float docPos = (dockR - dockL) * dockPer
						+ this.dockPosList.get(DOCK_L);
				this.dockPosList.put(dockPer, docPos);
			}
		}
		// Dock at current position
		this.xFrotStart = this.dockPosList.get(this.curDockPer);
		this.isInit = true;
		// For debug information
		StringBuilder sb = new StringBuilder();
		sb.append("BackImageW:" + this.backImage.getWidth() + "\n");
		sb.append("BackImageH:" + this.backImage.getHeight() + "\n");
		sb.append("FrotImageW:" + this.frotImage.getWidth() + "\n");
		sb.append("FrotImageH:" + this.frotImage.getHeight() + "\n");
		sb.append("xBackStart:" + xBackStart + "\n");
		sb.append("yBackStart:" + yBackStart + "\n");
		sb.append("xMarginLeft:" + xMarginLeft + "\n");
		sb.append("yMarginTop:" + yMarginTop + "\n");
		sb.append("xFrotInitP:" + xFrotInitPos + "\n");
		sb.append("yFrotInitP:" + yFrotInitPos + "\n");
		sb.append("xFrotStart:" + xFrotStart + "\n");
		sb.append("yFrotStart:" + yFrotStart + "\n");
		Log.v("SlipEntity", sb.toString());
	}
	 /**
	  * Weather the front image reaches the max right of background image, if
	  * true, set xFrotStart to max right.
	  */
	public boolean isReachRight() {
		if (this.xFrotStart > this.dockPosList.get(DOCK_R)) {
			this.curDockPer = DOCK_R;
			this.xFrotStart = this.dockPosList.get(DOCK_R);
			return true;
		} else {
			return false;
		}
	}
	 /**
	  * Weather the front image reaches the max left of background image, if
	  * true, set xFrotStart to max left.
	  */
	public boolean isReachLeft() {
		if (this.xFrotStart < this.dockPosList.get(DOCK_L)) {
			this.curDockPer = DOCK_L;
			this.xFrotStart = this.dockPosList.get(DOCK_L);
			return true;
		} else {
			return false;
		}
	}
	 /**
	  * Weather the point(x,y) is in the area of back or front image
	  * @param type
	  *            FLAG_FROT(front image) | FLAG_BACK(back image)
	  * @param x
	  *            X-coordinate of point
	  * @param y
	  *            Y-coordinate of point
	  * @return weather the point is in specified area
	  */
	public boolean isPointInImage(int type, float x, float y) {
		float rPointX;
		float rPointY;
		switch (type) {
		case FLAG_FROT:
			rPointX = this.xFrotStart + this.frotImage.getWidth();
			rPointY = this.yFrotStart + this.frotImage.getHeight();
			if (x > this.xFrotStart && y > this.yFrotStart && x < rPointX
					&& y < rPointY)
				return true;
			else
				return false;
		case FLAG_BACK:
			rPointX = this.xBackStart + this.backImage.getWidth();
			rPointY = this.yBackStart + this.backImage.getHeight();
			if (x > this.xBackStart && y > this.yBackStart && x < rPointX
					&& y < rPointY)
				return true;
			else
				return false;
		default:
			return false;
		}
	}
	 /**
	  * Is the current touch in some dock position
	  * @return return dockPer if in, or -1 while no match
	  */
	public float isDock() {
		for (float dockPer : this.dockPosList.keySet()) {
			float dockPos = this.dockPosList.get(dockPer);

			if (this.xFrotStart > dockPos - MICRO_X
					&& this.xFrotStart < dockPos + MICRO_X) {
				this.curDockPer = dockPer;
				return dockPer;
			}
		}
		return -1;
	}
	 /**
	  * Get the current dock percentage in x-axis
	  * @return
	  */
	public float getCurDockPer() {
		return this.curDockPer;
	}
	 /**
	  * Get the current dock position in x-axis
	  */
	public float getCurDockPos() {
		return this.dockPosList.get(this.curDockPer);
	}
	 /**
	  * Add dock position to the list
	  * @param dockPer
	  *            dock Percent should be between (0.0,1.0)
	  */
	public void addDockPos(float dockPer) {
		if (dockPer > 0 && dockPer < 1) {
			this.dockPosList.put(dockPer, (float) 0.0);
		}
	}
	 /**
	  * Return width of background image
	  * @return
	  */
	public float getBackWidth() {
		return this.backImage.getWidth();
	}
	 /**
	  * Return height of background image
	  */
	public float getBackHeight() {
		return this.backImage.getHeight();
	}
	 /**
	  * Return width of front image
	  */
	public float getFrotWidth() {
		return this.frotImage.getWidth();
	}
	 /**
	  * Return height of front image
	  */
	public float getFrotHeight() {
		return this.frotImage.getWidth();
	}
	 /**
	  * Dock at some position
	  * @param curDockPer
	  */
	public void setCurDockPos(float curDockPer) {
		this.curDockPer = curDockPer;
	}
	public Bitmap getBackImage() {
		return backImage;
	}
	public void setBackImage(Bitmap backImage) {
		this.backImage = backImage;
	}
	public Bitmap getFrotImage() {
		return frotImage;
	}
	public void setFrotImage(Bitmap frotImage) {
		this.frotImage = frotImage;
	}
	public float getxBackStart() {
		return xBackStart;
	}
	public void setxBackStart(float xBackStart) {
		this.xBackStart = xBackStart;
	}
	public float getyBackStart() {
		return yBackStart;
	}
	public void setyBackStart(float yBackStart) {
		this.yBackStart = yBackStart;
	}
	public float getxFrotStart() {
		return xFrotStart;
	}
	public void setxFrotStart(float xFrotStart) {
		this.xFrotStart = xFrotStart;
	}
	public float getyFrotStart() {
		return yFrotStart;
	}
	public void setyFrotStart(float yFrotStart) {
		this.yFrotStart = yFrotStart;
	}
	public float getxFrotInitPos() {
		return xFrotInitPos;
	}
	public void setxFrotInitPos(float xFrotInitPos) {
		this.xFrotInitPos = xFrotInitPos;
	}
	public float getyFrotInitPos() {
		return yFrotInitPos;
	}
	public void setyFrotInitPos(float yFrotInitPos) {
		this.yFrotInitPos = yFrotInitPos;
	}
	public float getxMarginLeft() {
		return xMarginLeft;
	}
	public void setxMarginLeft(float xMarginLeft) {
		this.xMarginLeft = xMarginLeft;
	}
	public float getyMarginTop() {
		return yMarginTop;
	}
	public void setyMarginTop(float yMarginTop) {
		this.yMarginTop = yMarginTop;
	}
	public Map<Float, Float> getDockPosList() {
		return dockPosList;
	}
	public void setDockPosList(Map<Float, Float> dockPosList) {
		this.dockPosList = dockPosList;
	}
	public boolean isInit() {
		return isInit;
	}
	public void setInit(boolean isInit) {
		this.isInit = isInit;
	}
}
```