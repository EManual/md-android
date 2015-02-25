有关SurfaceView相关的内容今天继续延用系统的示例类VideoView来让大家深入了解Android平台的图形绘制基础类的实现原理。大家可能会发现VideoView类的控制方面无法改变，我们可以通过重构VideoView类来实现更个性化的播放器
```  
public class VideoView extends SurfaceView implements MediaPlayerControl {
	private String TAG = "VideoView";
	// settable by the client
	private Uri mUri;
	private int mDuration;
	// all possible internal states
	private static final int STATE_ERROR = -1;
	private static final int STATE_IDLE = 0;
	private static final int STATE_PREPARING = 1;
	private static final int STATE_PREPARED = 2;
	private static final int STATE_PLAYING = 3;
	private static final int STATE_PAUSED = 4;
	private static final int STATE_PLAYBACK_COMPLETED = 5;
	// mCurrentState is a VideoView object's current state.
	// mTargetState is the state that a method caller intends to reach.
	// For instance, regardless the VideoView object's current state,
	// calling pause() intends to bring the object to a target state
	// of STATE_PAUSED.
	private int mCurrentState = STATE_IDLE;
	private int mTargetState = STATE_IDLE;
	// All the stuff we need for playing and showing a video
	private SurfaceHolder mSurfaceHolder = null;
	private MediaPlayer mMediaPlayer = null;
	private int mVideoWidth;
	private int mVideoHeight;
	private int mSurfaceWidth;
	private int mSurfaceHeight;
	private MediaController mMediaController;
	private OnCompletionListener mOnCompletionListener;
	private MediaPlayer.OnPreparedListener mOnPreparedListener;
	private int mCurrentBufferPercentage;
	private OnErrorListener mOnErrorListener;
	private int mSeekWhenPrepared; // recording the seek position while
									// preparing
	private boolean mCanPause;
	private boolean mCanSeekBack;
	private boolean mCanSeekForward;
	public VideoView(Context context) {
		super(context);
		initVideoView();
	}
	public VideoView(Context context, AttributeSet attrs) {
		this(context, attrs, 0);
		initVideoView();
	}
	public VideoView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		initVideoView();
	}
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		// Log.i("@@@@", "onMeasure");
		int width = getDefaultSize(mVideoWidth, widthMeasureSpec);
		int height = getDefaultSize(mVideoHeight, heightMeasureSpec);
		if (mVideoWidth > 0 && mVideoHeight > 0) {
			if (mVideoWidth * height > width * mVideoHeight) {
				// Log.i("@@@", "image too tall, correcting");
				height = width * mVideoHeight / mVideoWidth;
			} else if (mVideoWidth * height < width * mVideoHeight) {
				// Log.i("@@@", "image too wide, correcting");
				width = height * mVideoWidth / mVideoHeight;
			} else {
				// Log.i("@@@", "aspect ratio is correct: " +
				// width+"/"+height+"="+
				// mVideoWidth+"/"+mVideoHeight);
			}
		}
		// Log.i("@@@@@@@@@@", "setting size: " + width + 'x' + height);
		setMeasuredDimension(width, height);
	}
	public int resolveAdjustedSize(int desiredSize, int measureSpec) {
		int result = desiredSize;
		int specMode = MeasureSpec.getMode(measureSpec);
		int specSize = MeasureSpec.getSize(measureSpec);
		switch (specMode) {
		case MeasureSpec.UNSPECIFIED:
			/*
			 [Tags]* Parent says we can be as big as we want. Just don't be larger
			 [Tags]* than max size imposed on ourselves.
			 [Tags]*/
			result = desiredSize;
			break;
		case MeasureSpec.AT_MOST:
			/*
			 [Tags]* Parent says we can be as big as we want, up to specSize. Don't be
			 [Tags]* larger than specSize, and don't be larger than the max size
			 [Tags]* imposed on ourselves.
			 [Tags]*/
			result = Math.min(desiredSize, specSize);
			break;
		case MeasureSpec.EXACTLY:
			// No choice. Do what we are told.
			result = specSize;
			break;
		}
		return result;
	}
}
```