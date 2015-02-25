2. SlipEntity用于调整前景、背景图片位置等的操作。 
```  
import java.util.HashMap;
import java.util.Map;
import android.graphics.Bitmap;
import android.util.Log;
[Tags]/**
 [Tags]* Slip entity used to set position and maybe boundary of back and front image
 [Tags]*/
public class SlipEntity {
	public static final int FLAG_BACK = 1;
	public static final int FLAG_FROT = 2;
	public static final float DOCK_L = 0;
	public static final float DOCK_M = (float) 0.5;
	public static final float DOCK_R = 1;
	public static final float MICRO_X = 10;
	[Tags]/** Background image */
	Bitmap backImage;
	[Tags]/** Front image */
	Bitmap frotImage;
	[Tags]/** Start Position of back image */
	float xBackStart;
	[Tags]/** Start Position of back image */
	float yBackStart;
	[Tags]/** Start Position of front image */
	float xFrotStart;
	[Tags]/** Start Position of front image */
	float yFrotStart;
	[Tags]/** initial Position of front image */
	float xFrotInitPos;
	[Tags]/** initial Position of front image */
	float yFrotInitPos;
	[Tags]/** Margin of front and back image in X-Axis */
	float xMarginLeft;
	[Tags]/** Margin of front and back image in Y-Axis */
	float yMarginTop;
	[Tags]/** Containing dock position of the front image */
	Map<Float, Float> dockPosList = new HashMap<Float, Float>();
	[Tags]/** Current dock Percentage: DOCK_L | DOCK_M | DOCK_R */
	float curDockPer = DOCK_L;
	[Tags]/** Weather has invoked initSlipEntity() */
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
	[Tags]/**
	 [Tags]* Weather the front image reaches the max right of background image, if
	 [Tags]* true, set xFrotStart to max right.
	 [Tags]*/
	public boolean isReachRight() {
		if (this.xFrotStart > this.dockPosList.get(DOCK_R)) {
			this.curDockPer = DOCK_R;
			this.xFrotStart = this.dockPosList.get(DOCK_R);
			return true;
		} else {
			return false;
		}
	}
	[Tags]/**
	 [Tags]* Weather the front image reaches the max left of background image, if
	 [Tags]* true, set xFrotStart to max left.
	 [Tags]*/
	public boolean isReachLeft() {
		if (this.xFrotStart < this.dockPosList.get(DOCK_L)) {
			this.curDockPer = DOCK_L;
			this.xFrotStart = this.dockPosList.get(DOCK_L);
			return true;
		} else {
			return false;
		}
	}
	[Tags]/**
	 [Tags]* Weather the point(x,y) is in the area of back or front image
	 [Tags]* @param type
	 [Tags]*            FLAG_FROT(front image) | FLAG_BACK(back image)
	 [Tags]* @param x
	 [Tags]*            X-coordinate of point
	 [Tags]* @param y
	 [Tags]*            Y-coordinate of point
	 [Tags]* @return weather the point is in specified area
	 [Tags]*/
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
	[Tags]/**
	 [Tags]* Is the current touch in some dock position
	 [Tags]* @return return dockPer if in, or -1 while no match
	 [Tags]*/
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
	[Tags]/**
	 [Tags]* Get the current dock percentage in x-axis
	 [Tags]* @return
	 [Tags]*/
	public float getCurDockPer() {
		return this.curDockPer;
	}
	[Tags]/**
	 [Tags]* Get the current dock position in x-axis
	 [Tags]*/
	public float getCurDockPos() {
		return this.dockPosList.get(this.curDockPer);
	}
	[Tags]/**
	 [Tags]* Add dock position to the list
	 [Tags]* @param dockPer
	 [Tags]*            dock Percent should be between (0.0,1.0)
	 [Tags]*/
	public void addDockPos(float dockPer) {
		if (dockPer > 0 && dockPer < 1) {
			this.dockPosList.put(dockPer, (float) 0.0);
		}
	}
	[Tags]/**
	 [Tags]* Return width of background image
	 [Tags]* @return
	 [Tags]*/
	public float getBackWidth() {
		return this.backImage.getWidth();
	}
	[Tags]/**
	 [Tags]* Return height of background image
	 [Tags]*/
	public float getBackHeight() {
		return this.backImage.getHeight();
	}
	[Tags]/**
	 [Tags]* Return width of front image
	 [Tags]*/
	public float getFrotWidth() {
		return this.frotImage.getWidth();
	}
	[Tags]/**
	 [Tags]* Return height of front image
	 [Tags]*/
	public float getFrotHeight() {
		return this.frotImage.getWidth();
	}
	[Tags]/**
	 [Tags]* Dock at some position
	 [Tags]* @param curDockPer
	 [Tags]*/
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