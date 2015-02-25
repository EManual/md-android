有关SurfaceView我们将通过三个系统自带的例子来深入掌握Android绘图必会的SurfaceView，今天我们以SDK中的Sample游戏lunarlander中的LunarView具体实现，建议大家导入该游戏工程到你的Eclipse然后自己编译先玩一下这个游戏，然后再看代码比较好理解。
```  
class LunarView extends SurfaceView implements SurfaceHolder.Callback {
	class LunarThread extends Thread {
		/*
		 [Tags]* Difficulty setting constants
		 [Tags]*/
		public static final int DIFFICULTY_EASY = 0;
		public static final int DIFFICULTY_HARD = 1;
		public static final int DIFFICULTY_MEDIUM = 2;
		/*
		 [Tags]* Physics constants
		 [Tags]*/
		public static final int PHYS_DOWN_ACCEL_SEC = 35;
		public static final int PHYS_FIRE_ACCEL_SEC = 80;
		public static final int PHYS_FUEL_INIT = 60;
		public static final int PHYS_FUEL_MAX = 100;
		public static final int PHYS_FUEL_SEC = 10;
		public static final int PHYS_SLEW_SEC = 120; // degrees/second rotate
		public static final int PHYS_SPEED_HYPERSPACE = 180;
		public static final int PHYS_SPEED_INIT = 30;
		public static final int PHYS_SPEED_MAX = 120;
		/*
		 [Tags]* State-tracking constants
		 [Tags]*/
		public static final int STATE_LOSE = 1;
		public static final int STATE_PAUSE = 2;
		public static final int STATE_READY = 3;
		public static final int STATE_RUNNING = 4;
		public static final int STATE_WIN = 5;
		/*
		 [Tags]* Goal condition constants
		 [Tags]*/
		public static final int TARGET_ANGLE = 18; // > this angle means crash
		public static final int TARGET_BOTTOM_PADDING = 17; // px below gear
		public static final int TARGET_PAD_HEIGHT = 8; // how high above ground
		public static final int TARGET_SPEED = 28; // > this speed means crash
		public static final double TARGET_WIDTH = 1.6; // width of target
		/*
		 [Tags]* UI constants (i.e. the speed & fuel bars)
		 [Tags]*/
		public static final int UI_BAR = 100; // width of the bar(s)
		public static final int UI_BAR_HEIGHT = 10; // height of the bar(s)
		private static final String KEY_DIFFICULTY = "mDifficulty";
		private static final String KEY_DX = "mDX";
		private static final String KEY_DY = "mDY";
		private static final String KEY_FUEL = "mFuel";
		private static final String KEY_GOAL_ANGLE = "mGoalAngle";
		private static final String KEY_GOAL_SPEED = "mGoalSpeed";
		private static final String KEY_GOAL_WIDTH = "mGoalWidth";
		private static final String KEY_GOAL_X = "mGoalX";
		private static final String KEY_HEADING = "mHeading";
		private static final String KEY_LANDER_HEIGHT = "mLanderHeight";
		private static final String KEY_LANDER_WIDTH = "mLanderWidth";
		private static final String KEY_WINS = "mWinsInARow";
		private static final String KEY_X = "mX";
		private static final String KEY_Y = "mY";
		/*
		 [Tags]* Member (state) fields
		 [Tags]*/
		[Tags]/** The drawable to use as the background of the animation canvas */
		private Bitmap mBackgroundImage;

		[Tags]/**
		 [Tags]* Current height of the surface/canvas.
		 [Tags]* 
		 [Tags]* @see setSurfaceSize
		 [Tags]*/
		private int mCanvasHeight = 1;

		[Tags]/**
		 [Tags]* Current width of the surface/canvas.
		 [Tags]* 
		 [Tags]* @see setSurfaceSize
		 [Tags]*/
		private int mCanvasWidth = 1;

		[Tags]/** What to draw for the Lander when it has crashed */
		private Drawable mCrashedImage;

		[Tags]/**
		 [Tags]* Current difficulty -- amount of fuel, allowed angle, etc. Default is
		 [Tags]* MEDIUM.
		 [Tags]*/
		private int mDifficulty;

		[Tags]/** Velocity dx. */
		private double mDX;

		[Tags]/** Velocity dy. */
		private double mDY;

		[Tags]/** Is the engine burning? */
		private boolean mEngineFiring;

		[Tags]/** What to draw for the Lander when the engine is firing */
		private Drawable mFiringImage;

		[Tags]/** Fuel remaining */
		private double mFuel;

		[Tags]/** Allowed angle. */
		private int mGoalAngle;

		[Tags]/** Allowed speed. */
		private int mGoalSpeed;

		[Tags]/** Width of the landing pad. */
		private int mGoalWidth;

		[Tags]/** X of the landing pad. */
		private int mGoalX;

		[Tags]/** Message handler used by thread to interact with TextView */
		private Handler mHandler;

		[Tags]/**
		 [Tags]* Lander heading in degrees, with 0 up, 90 right. Kept in the range
		 [Tags]* 0..360.
		 [Tags]*/
		private double mHeading;

		[Tags]/** Pixel height of lander image. */
		private int mLanderHeight;

		[Tags]/** What to draw for the Lander in its normal state */
		private Drawable mLanderImage;

		[Tags]/** Pixel width of lander image. */
		private int mLanderWidth;

		[Tags]/** Used to figure out elapsed time between frames */
		private long mLastTime;

		[Tags]/** Paint to draw the lines on screen. */
		private Paint mLinePaint;

		[Tags]/** "Bad" speed-too-high variant of the line color. */
		private Paint mLinePaintBad;

		[Tags]/** The state of the game. One of READY, RUNNING, PAUSE, LOSE, or WIN */
		private int mMode;

		[Tags]/** Currently rotating, -1 left, 0 none, 1 right. */
		private int mRotating;

		[Tags]/** Indicate whether the surface has been created & is ready to draw */
		private boolean mRun = false;

		[Tags]/** Scratch rect object. */
		private RectF mScratchRect;

		[Tags]/** Handle to the surface manager object we interact with */
		private SurfaceHolder mSurfaceHolder;

		[Tags]/** Number of wins in a row. */
		private int mWinsInARow;

		[Tags]/** X of lander center. */
		private double mX;

		[Tags]/** Y of lander center. */
		private double mY;

		public LunarThread(SurfaceHolder surfaceHolder, Context context,
				Handler handler) {
			// get handles to some important objects
			mSurfaceHolder = surfaceHolder;
			mHandler = handler;
			mContext = context;

			Resources res = context.getResources();
			// cache handles to our key sprites & other drawables
			mLanderImage = context.getResources().getDrawable(
					R.drawable.lander_plain);
			mFiringImage = context.getResources().getDrawable(
					R.drawable.lander_firing);
			mCrashedImage = context.getResources().getDrawable(
					R.drawable.lander_crashed);

			// load background image as a Bitmap instead of a Drawable b/c
			// we don't need to transform it and it's faster to draw this way
			mBackgroundImage = BitmapFactory.decodeResource(res,
					R.drawable.earthrise);

			// Use the regular lander image as the model size for all sprites
			mLanderWidth = mLanderImage.getIntrinsicWidth();
			mLanderHeight = mLanderImage.getIntrinsicHeight();

			// Initialize paints for speedometer
			mLinePaint = new Paint();
			mLinePaint.setAntiAlias(true);
			mLinePaint.setARGB(255, 0, 255, 0);

			mLinePaintBad = new Paint();
			mLinePaintBad.setAntiAlias(true);
			mLinePaintBad.setARGB(255, 120, 180, 0);

			mScratchRect = new RectF(0, 0, 0, 0);

			mWinsInARow = 0;
			mDifficulty = DIFFICULTY_MEDIUM;

			// initial show-up of lander (not yet playing)
			mX = mLanderWidth;
			mY = mLanderHeight * 2;
			mFuel = PHYS_FUEL_INIT;
			mDX = 0;
			mDY = 0;
			mHeading = 0;
			mEngineFiring = true;
		}

		[Tags]/**
		 [Tags]* Starts the game, setting parameters for the current difficulty.
		 [Tags]*/
		public void doStart() {
			synchronized (mSurfaceHolder) {
				// First set the game for Medium difficulty
				mFuel = PHYS_FUEL_INIT;
				mEngineFiring = false;
				mGoalWidth = (int) (mLanderWidth * TARGET_WIDTH);
				mGoalSpeed = TARGET_SPEED;
				mGoalAngle = TARGET_ANGLE;
				int speedInit = PHYS_SPEED_INIT;

				// Adjust difficulty params for EASY/HARD
				if (mDifficulty == DIFFICULTY_EASY) {
					mFuel = mFuel * 3 / 2;
					mGoalWidth = mGoalWidth * 4 / 3;
					mGoalSpeed = mGoalSpeed * 3 / 2;
					mGoalAngle = mGoalAngle * 4 / 3;
					speedInit = speedInit * 3 / 4;
				} else if (mDifficulty == DIFFICULTY_HARD) {
					mFuel = mFuel * 7 / 8;
					mGoalWidth = mGoalWidth * 3 / 4;
					mGoalSpeed = mGoalSpeed * 7 / 8;
					speedInit = speedInit * 4 / 3;
				}

				// pick a convenient initial location for the lander sprite
				mX = mCanvasWidth / 2;
				mY = mCanvasHeight - mLanderHeight / 2;

				// start with a little random motion
				mDY = Math.random() * -speedInit;
				mDX = Math.random() * 2 * speedInit - speedInit;
				mHeading = 0;

				// Figure initial spot for landing, not too near center
				while (true) {
					mGoalX = (int) (Math.random() * (mCanvasWidth - mGoalWidth));
					if (Math.abs(mGoalX - (mX - mLanderWidth / 2)) > mCanvasHeight / 6)
						break;
				}

				mLastTime = System.currentTimeMillis() + 100;
				setState(STATE_RUNNING);
			}
		}

		[Tags]/**
		 [Tags]* Pauses the physics update & animation.
		 [Tags]*/
		public void pause() {
			synchronized (mSurfaceHolder) {
				if (mMode == STATE_RUNNING)
					setState(STATE_PAUSE);
			}
		}

		[Tags]/**
		 [Tags]* Restores game state from the indicated Bundle. Typically called when
		 [Tags]* the Activity is being restored after having been previously
		 [Tags]* destroyed.
		 [Tags]* 
		 [Tags]* @param savedState
		 [Tags]*            Bundle containing the game state
		 [Tags]*/
		public synchronized void restoreState(Bundle savedState) {
			synchronized (mSurfaceHolder) {
				setState(STATE_PAUSE);
				mRotating = 0;
				mEngineFiring = false;

				mDifficulty = savedState.getInt(KEY_DIFFICULTY);
				mX = savedState.getDouble(KEY_X);
				mY = savedState.getDouble(KEY_Y);
				mDX = savedState.getDouble(KEY_DX);
				mDY = savedState.getDouble(KEY_DY);
				mHeading = savedState.getDouble(KEY_HEADING);

				mLanderWidth = savedState.getInt(KEY_LANDER_WIDTH);
				mLanderHeight = savedState.getInt(KEY_LANDER_HEIGHT);
				mGoalX = savedState.getInt(KEY_GOAL_X);
				mGoalSpeed = savedState.getInt(KEY_GOAL_SPEED);
				mGoalAngle = savedState.getInt(KEY_GOAL_ANGLE);
				mGoalWidth = savedState.getInt(KEY_GOAL_WIDTH);
				mWinsInARow = savedState.getInt(KEY_WINS);
				mFuel = savedState.getDouble(KEY_FUEL);
			}
		}

		@Override
		public void run() {
			while (mRun) {
				Canvas c = null;
				try {
					c = mSurfaceHolder.lockCanvas(null);
					synchronized (mSurfaceHolder) {
						if (mMode == STATE_RUNNING)
							updatePhysics();
						doDraw(c);
					}
				} finally {
					// do this in a finally so that if an exception is thrown
					// during the above, we don't leave the Surface in an
					// inconsistent state
					if (c != null) {
						mSurfaceHolder.unlockCanvasAndPost(c);
					}
				}
			}
		}

		[Tags]/**
		 [Tags]* Dump game state to the provided Bundle. Typically called when the
		 [Tags]* Activity is being suspended.
		 [Tags]* 
		 [Tags]* @return Bundle with this view's state
		 [Tags]*/
		public Bundle saveState(Bundle map) {
			synchronized (mSurfaceHolder) {
				if (map != null) {
					map.putInt(KEY_DIFFICULTY, Integer.valueOf(mDifficulty));
					map.putDouble(KEY_X, Double.valueOf(mX));
					map.putDouble(KEY_Y, Double.valueOf(mY));
					map.putDouble(KEY_DX, Double.valueOf(mDX));
					map.putDouble(KEY_DY, Double.valueOf(mDY));
					map.putDouble(KEY_HEADING, Double.valueOf(mHeading));
					map.putInt(KEY_LANDER_WIDTH, Integer.valueOf(mLanderWidth));
					map.putInt(KEY_LANDER_HEIGHT,
							Integer.valueOf(mLanderHeight));
					map.putInt(KEY_GOAL_X, Integer.valueOf(mGoalX));
					map.putInt(KEY_GOAL_SPEED, Integer.valueOf(mGoalSpeed));
					map.putInt(KEY_GOAL_ANGLE, Integer.valueOf(mGoalAngle));
					map.putInt(KEY_GOAL_WIDTH, Integer.valueOf(mGoalWidth));
					map.putInt(KEY_WINS, Integer.valueOf(mWinsInARow));
					map.putDouble(KEY_FUEL, Double.valueOf(mFuel));
				}
			}
			return map;
		}

		[Tags]/**
		 [Tags]* Sets the current difficulty.
		 [Tags]* 
		 [Tags]* @param difficulty
		 [Tags]*/
		public void setDifficulty(int difficulty) {
			synchronized (mSurfaceHolder) {
				mDifficulty = difficulty;
			}
		}

		[Tags]/**
		 [Tags]* Sets if the engine is currently firing.
		 [Tags]*/
		public void setFiring(boolean firing) {
			synchronized (mSurfaceHolder) {
				mEngineFiring = firing;
			}
		}

		[Tags]/**
		 [Tags]* Used to signal the thread whether it should be running or not.
		 [Tags]* Passing true allows the thread to run; passing false will shut it
		 [Tags]* down if it's already running. Calling start() after this was most
		 [Tags]* recently called with false will result in an immediate shutdown.
		 [Tags]* 
		 [Tags]* @param b
		 [Tags]*            true to run, false to shut down
		 [Tags]*/
		public void setRunning(boolean b) {
			mRun = b;
		}

		[Tags]/**
		 [Tags]* Sets the game mode. That is, whether we are running, paused, in the
		 [Tags]* failure state, in the victory state, etc.
		 [Tags]* 
		 [Tags]* @see setState(int, CharSequence)
		 [Tags]* @param mode
		 [Tags]*            one of the STATE_[Tags]* constants
		 [Tags]*/
		public void setState(int mode) {
			synchronized (mSurfaceHolder) {
				setState(mode, null);
			}
		}

		[Tags]/**
		 [Tags]* Sets the game mode. That is, whether we are running, paused, in the
		 [Tags]* failure state, in the victory state, etc.
		 [Tags]* 
		 [Tags]* @param mode
		 [Tags]*            one of the STATE_[Tags]* constants
		 [Tags]* @param message
		 [Tags]*            string to add to screen or null
		 [Tags]*/
		public void setState(int mode, CharSequence message) {
			/*
			 [Tags]* This method optionally can cause a text message to be displayed
			 [Tags]* to the user when the mode changes. Since the View that actually
			 [Tags]* renders that text is part of the main View hierarchy and not
			 [Tags]* owned by this thread, we can't touch the state of that View.
			 [Tags]* Instead we use a Message + Handler to relay commands to the main
			 [Tags]* thread, which updates the user-text View.
			 [Tags]*/
			synchronized (mSurfaceHolder) {
				mMode = mode;

				if (mMode == STATE_RUNNING) {
					Message msg = mHandler.obtainMessage();
					Bundle b = new Bundle();
					b.putString("text", "");
					b.putInt("viz", View.INVISIBLE);
					msg.setData(b);
					mHandler.sendMessage(msg);
				} else {
					mRotating = 0;
					mEngineFiring = false;
					Resources res = mContext.getResources();
					CharSequence str = "";
					if (mMode == STATE_READY)
						str = res.getText(R.string.mode_ready);
					else if (mMode == STATE_PAUSE)
						str = res.getText(R.string.mode_pause);
					else if (mMode == STATE_LOSE)
						str = res.getText(R.string.mode_lose);
					else if (mMode == STATE_WIN)
						str = res.getString(R.string.mode_win_prefix)
								+ mWinsInARow + "
								+ res.getString(R.string.mode_win_suffix);

					if (message != null) {
						str = message + "\n" + str;
					}

					if (mMode == STATE_LOSE)
						mWinsInARow = 0;

					Message msg = mHandler.obtainMessage();
					Bundle b = new Bundle();
					b.putString("text", str.toString());
					b.putInt("viz", View.VISIBLE);
					msg.setData(b);
					mHandler.sendMessage(msg);
				}
			}
		}

		/* Callback invoked when the surface dimensions change. */
		public void setSurfaceSize(int width, int height) {
			// synchronized to make sure these all change atomically
			synchronized (mSurfaceHolder) {
				mCanvasWidth = width;
				mCanvasHeight = height;

				// don't forget to resize the background image
				mBackgroundImage = mBackgroundImage.createScaledBitmap(
						mBackgroundImage, width, height, true);
			}
		}

		[Tags]/**
		 [Tags]* Resumes from a pause.
		 [Tags]*/
		public void unpause() {
			// Move the real time clock up to now
			synchronized (mSurfaceHolder) {
				mLastTime = System.currentTimeMillis() + 100;
			}
			setState(STATE_RUNNING);
		}

		[Tags]/**
		 [Tags]* Handles a key-down event.
		 [Tags]* 
		 [Tags]* @param keyCode
		 [Tags]*            the key that was pressed
		 [Tags]* @param msg
		 [Tags]*            the original event object
		 [Tags]* @return true
		 [Tags]*/
		boolean doKeyDown(int keyCode, KeyEvent msg) {
			synchronized (mSurfaceHolder) {
				boolean okStart = false;
				if (keyCode == KeyEvent.KEYCODE_DPAD_UP)
					okStart = true;
				if (keyCode == KeyEvent.KEYCODE_DPAD_DOWN)
					okStart = true;
				if (keyCode == KeyEvent.KEYCODE_S)
					okStart = true;
				boolean center = (keyCode == KeyEvent.KEYCODE_DPAD_UP);
				if (okStart
						&& (mMode == STATE_READY || mMode == STATE_LOSE || mMode == STATE_WIN)) {
					// ready-to-start -> start
					doStart();
					return true;
				} else if (mMode == STATE_PAUSE && okStart) {
					// paused -> running
					unpause();
					return true;
				} else if (mMode == STATE_RUNNING) {
					// center/space -> fire
					if (keyCode == KeyEvent.KEYCODE_DPAD_CENTER
							|| keyCode == KeyEvent.KEYCODE_SPACE) {
						setFiring(true);
						return true;
						// left/q -> left
					} else if (keyCode == KeyEvent.KEYCODE_DPAD_LEFT
							|| keyCode == KeyEvent.KEYCODE_Q) {
						mRotating = -1;
						return true;
						// right/w -> right
					} else if (keyCode == KeyEvent.KEYCODE_DPAD_RIGHT
							|| keyCode == KeyEvent.KEYCODE_W) {
						mRotating = 1;
						return true;
						// up -> pause
					} else if (keyCode == KeyEvent.KEYCODE_DPAD_UP) {
						pause();
						return true;
					}
				}
				return false;
			}
		}

		[Tags]/**
		 [Tags]* Handles a key-up event.
		 [Tags]* 
		 [Tags]* @param keyCode
		 [Tags]*            the key that was pressed
		 [Tags]* @param msg
		 [Tags]*            the original event object
		 [Tags]* @return true if the key was handled and consumed, or else false
		 [Tags]*/
		boolean doKeyUp(int keyCode, KeyEvent msg) {
			boolean handled = false;
			synchronized (mSurfaceHolder) {
				if (mMode == STATE_RUNNING) {
					if (keyCode == KeyEvent.KEYCODE_DPAD_CENTER
							|| keyCode == KeyEvent.KEYCODE_SPACE) {
						setFiring(false);
						handled = true;
					} else if (keyCode == KeyEvent.KEYCODE_DPAD_LEFT
							|| keyCode == KeyEvent.KEYCODE_Q
							|| keyCode == KeyEvent.KEYCODE_DPAD_RIGHT
							|| keyCode == KeyEvent.KEYCODE_W) {
						mRotating = 0;
						handled = true;
					}
				}
			}
			return handled;
		}

		[Tags]/**
		 [Tags]* Draws the ship, fuel/speed bars, and background to the provided
		 [Tags]* Canvas.
		 [Tags]*/
		private void doDraw(Canvas canvas) {
			// Draw the background image. Operations on the Canvas accumulate
			// so this is like clearing the screen.
			canvas.drawBitmap(mBackgroundImage, 0, 0, null);
			int yTop = mCanvasHeight - ((int) mY + mLanderHeight / 2);
			int xLeft = (int) mX - mLanderWidth / 2;
			// Draw the fuel gauge
			int fuelWidth = (int) (UI_BAR * mFuel / PHYS_FUEL_MAX);
			mScratchRect.set(4, 4, 4 + fuelWidth, 4 + UI_BAR_HEIGHT);
			canvas.drawRect(mScratchRect, mLinePaint);
			// Draw the speed gauge, with a two-tone effect
			double speed = Math.sqrt(mDX * mDX + mDY * mDY);
			int speedWidth = (int) (UI_BAR * speed / PHYS_SPEED_MAX);
			if (speed <= mGoalSpeed) {
				mScratchRect.set(4 + UI_BAR + 4, 4,
						4 + UI_BAR + 4 + speedWidth, 4 + UI_BAR_HEIGHT);
				canvas.drawRect(mScratchRect, mLinePaint);
			} else {
				// Draw the bad color in back, with the good color in front of
				// it
				mScratchRect.set(4 + UI_BAR + 4, 4,
						4 + UI_BAR + 4 + speedWidth, 4 + UI_BAR_HEIGHT);
				canvas.drawRect(mScratchRect, mLinePaintBad);
				int goalWidth = (UI_BAR * mGoalSpeed / PHYS_SPEED_MAX);
				mScratchRect.set(4 + UI_BAR + 4, 4, 4 + UI_BAR + 4 + goalWidth,
						4 + UI_BAR_HEIGHT);
				canvas.drawRect(mScratchRect, mLinePaint);
			}
			// Draw the landing pad
			canvas.drawLine(mGoalX, 1 + mCanvasHeight - TARGET_PAD_HEIGHT,
					mGoalX + mGoalWidth, 1 + mCanvasHeight - TARGET_PAD_HEIGHT,
					mLinePaint);
			// Draw the ship with its current rotation
			canvas.save();
			canvas.rotate((float) mHeading, (float) mX, mCanvasHeight
					- (float) mY);
			if (mMode == STATE_LOSE) {
				mCrashedImage.setBounds(xLeft, yTop, xLeft + mLanderWidth, yTop
						+ mLanderHeight);
				mCrashedImage.draw(canvas);
			} else if (mEngineFiring) {
				mFiringImage.setBounds(xLeft, yTop, xLeft + mLanderWidth, yTop
						+ mLanderHeight);
				mFiringImage.draw(canvas);
			} else {
				mLanderImage.setBounds(xLeft, yTop, xLeft + mLanderWidth, yTop
						+ mLanderHeight);
				mLanderImage.draw(canvas);
			}
			canvas.restore();
		}

		[Tags]/**
		 [Tags]* Figures the lander state (x, y, fuel, ...) based on the passage of
		 [Tags]* realtime. Does not invalidate(). Called at the start of draw().
		 [Tags]* Detects the end-of-game and sets the UI to the next state.
		 [Tags]*/
		private void updatePhysics() {
			long now = System.currentTimeMillis();
			// Do nothing if mLastTime is in the future.
			// This allows the game-start to delay the start of the physics
			// by 100ms or whatever.
			if (mLastTime > now)
				return;
			double elapsed = (now - mLastTime) / 1000.0;
			// mRotating -- update heading
			if (mRotating != 0) {
				mHeading += mRotating * (PHYS_SLEW_SEC * elapsed);
				// Bring things back into the range 0..360
				if (mHeading < 0)
					mHeading += 360;
				else if (mHeading >= 360)
					mHeading -= 360;
			}
			// Base accelerations -- 0 for x, gravity for y
			double ddx = 0.0;
			double ddy = -PHYS_DOWN_ACCEL_SEC * elapsed;
			if (mEngineFiring) {
				// taking 0 as up, 90 as to the right
				// cos(deg) is ddy component, sin(deg) is ddx component
				double elapsedFiring = elapsed;
				double fuelUsed = elapsedFiring * PHYS_FUEL_SEC;
				// tricky case where we run out of fuel partway through the
				// elapsed
				if (fuelUsed > mFuel) {
					elapsedFiring = mFuel / fuelUsed * elapsed;
					fuelUsed = mFuel;
					// Oddball case where we adjust the "control" from here
					mEngineFiring = false;
				}
				mFuel -= fuelUsed;
				// have this much acceleration from the engine
				double accel = PHYS_FIRE_ACCEL_SEC * elapsedFiring;
				double radians = 2 * Math.PI * mHeading / 360;
				ddx = Math.sin(radians) * accel;
				ddy += Math.cos(radians) * accel;
			}
			double dxOld = mDX;
			double dyOld = mDY;
			// figure speeds for the end of the period
			mDX += ddx;
			mDY += ddy;
			// figure position based on average speed during the period
			mX += elapsed * (mDX + dxOld) / 2;
			mY += elapsed * (mDY + dyOld) / 2;
			mLastTime = now;
			// Evaluate if we have landed ... stop the game
			double yLowerBound = TARGET_PAD_HEIGHT + mLanderHeight / 2
					- TARGET_BOTTOM_PADDING;
			if (mY <= yLowerBound) {
				mY = yLowerBound;
				int result = STATE_LOSE;
				CharSequence message = "";
				Resources res = mContext.getResources();
				double speed = Math.sqrt(mDX * mDX + mDY * mDY);
				boolean onGoal = (mGoalX <= mX - mLanderWidth / 2 && mX
						+ mLanderWidth / 2 <= mGoalX + mGoalWidth);
				// "Hyperspace" win -- upside down, going fast,
				// puts you back at the top.
				if (onGoal && Math.abs(mHeading - 180) < mGoalAngle
						&& speed > PHYS_SPEED_HYPERSPACE) {
					result = STATE_WIN;
					mWinsInARow++;
					doStart();
					return;
					// Oddball case: this case does a return, all other cases
					// fall through to setMode() below.
				} else if (!onGoal) {
					message = res.getText(R.string.message_off_pad);
				} else if (!(mHeading <= mGoalAngle || mHeading >= 360 - mGoalAngle)) {
					message = res.getText(R.string.message_bad_angle);
				} else if (speed > mGoalSpeed) {
					message = res.getText(R.string.message_too_fast);
				} else {
					result = STATE_WIN;
					mWinsInARow++;
				}
				setState(result, message);
			}
		}
	}

	[Tags]/** Handle to the application context, used to e.g. fetch Drawables. */
	private Context mContext;
	[Tags]/** Pointer to the text view to display "Paused.." etc. */
	private TextView mStatusText;
	[Tags]/** The thread that actually draws the animation */
	private LunarThread thread;

	public LunarView(Context context, AttributeSet attrs) {
		super(context, attrs);
		// register our interest in hearing about changes to our surface
		SurfaceHolder holder = getHolder();
		holder.addCallback(this);

		// create thread only; it's started in surfaceCreated()
		thread = new LunarThread(holder, context, new Handler() {
			@Override
			public void handleMessage(Message m) {
				mStatusText.setVisibility(m.getData().getInt("viz"));
				mStatusText.setText(m.getData().getString("text"));
			}
		});
		setFocusable(true); // make sure we get key events
	}

	[Tags]/**
	 [Tags]* Fetches the animation thread corresponding to this LunarView.
	 [Tags]* 
	 [Tags]* @return the animation thread
	 [Tags]*/
	public LunarThread getThread() {
		return thread;
	}

	[Tags]/**
	 [Tags]* Standard override to get key-press events.
	 [Tags]*/
	@Override
	public boolean onKeyDown(int keyCode, KeyEvent msg) {
		return thread.doKeyDown(keyCode, msg);
	}

	[Tags]/**
	 [Tags]* Standard override for key-up. We actually care about these, so we can
	 [Tags]* turn off the engine or stop rotating.
	 [Tags]*/
	@Override
	public boolean onKeyUp(int keyCode, KeyEvent msg) {
		return thread.doKeyUp(keyCode, msg);
	}

	[Tags]/**
	 [Tags]* Standard window-focus override. Notice focus lost so we can pause on
	 [Tags]* focus lost. e.g. user switches to take a call.
	 [Tags]*/
	@Override
	public void onWindowFocusChanged(boolean hasWindowFocus) {
		if (!hasWindowFocus)
			thread.pause();
	}

	[Tags]/**
	 [Tags]* Installs a pointer to the text view used for messages.
	 [Tags]*/
	public void setTextView(TextView textView) {
		mStatusText = textView;
	}

	/* Callback invoked when the surface dimensions change. */
	public void surfaceChanged(SurfaceHolder holder, int format, int width,
			int height) {
		thread.setSurfaceSize(width, height);
	}

	/*
	 [Tags]* Callback invoked when the Surface has been created and is ready to be
	 [Tags]* used.
	 [Tags]*/
	public void surfaceCreated(SurfaceHolder holder) {
		// start the thread here so that we don't busy-wait in run()
		// waiting for the surface to be created
		thread.setRunning(true);
		thread.start();
	}

	/*
	 [Tags]* Callback invoked when the Surface has been destroyed and must no longer
	 [Tags]* be touched. WARNING: after this method returns, the Surface/Canvas must
	 [Tags]* never be touched again!
	 [Tags]*/
	public void surfaceDestroyed(SurfaceHolder holder) {
		// we have to tell thread to shut down & wait for it to finish, or else
		// it might touch the Surface after we return and explode
		boolean retry = true;
		thread.setRunning(false);
		while (retry) {
			try {
				thread.join();
				retry = false;
			} catch (InterruptedException e) {
			}
		}
	}
}
```