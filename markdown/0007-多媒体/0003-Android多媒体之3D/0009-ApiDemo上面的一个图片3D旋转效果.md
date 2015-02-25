源码如下：
```  
/*
 [Tags]* Copyright (C) 2007 The Android Open Source Project
 [Tags]* Licensed under the Apache License, Version 2.0 (the "License");
 [Tags]* you may not use this file except in compliance with the License.
 [Tags]* You may obtain a copy of the License at
 [Tags]*      http://www.apache.org/licenses/LICENSE-2.0
 [Tags]* Unless required by applicable law or agreed to in writing, software
 [Tags]* distributed under the License is distributed on an "AS IS" BASIS,
 [Tags]* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 [Tags]* See the License for the specific language governing permissions and
 [Tags]* limitations under the License.
 [Tags]*/
import android.view.animation.Animation;
import android.view.animation.Transformation;
import android.graphics.Camera;
import android.graphics.Matrix;
[Tags]/**
 [Tags]* An animation that rotates the view on the Y axis between two specified
 [Tags]* angles. This animation also adds a translation on the Z axis (depth) to
 [Tags]* improve the effect.
 [Tags]*/
public class Rotate3dAnimation extends Animation {
	private final float mFromDegrees;
	private final float mToDegrees;
	private final float mCenterX;
	private final float mCenterY;
	private final float mDepthZ;
	private final boolean mReverse;
	private Camera mCamera;
	[Tags]/**
	 [Tags]* Creates a new 3D rotation on the Y axis. The rotation is defined by its
	 [Tags]* start angle and its end angle. Both angles are in degrees. The rotation
	 [Tags]* is performed around a center point on the 2D space, definied by a pair of
	 [Tags]* X and Y coordinates, called centerX and centerY. When the animation
	 [Tags]* starts, a translation on the Z axis (depth) is performed. The length of
	 [Tags]* the translation can be specified, as well as whether the translation
	 [Tags]* should be reversed in time.
	 [Tags]* @param fromDegrees
	 [Tags]*            the start angle of the 3D rotation
	 [Tags]* @param toDegrees
	 [Tags]*            the end angle of the 3D rotation
	 [Tags]* @param centerX
	 [Tags]*            the X center of the 3D rotation
	 [Tags]* @param centerY
	 [Tags]*            the Y center of the 3D rotation
	 [Tags]* @param reverse
	 [Tags]*            true if the translation should be reversed, false otherwise
	 [Tags]*/
	public Rotate3dAnimation(float fromDegrees, float toDegrees, float centerX,
			float centerY, float depthZ, boolean reverse) {
		mFromDegrees = fromDegrees;
		mToDegrees = toDegrees;
		mCenterX = centerX;
		mCenterY = centerY;
		mDepthZ = depthZ;
		mReverse = reverse;
	}
	@Override
	public void initialize(int width, int height, int parentWidth,
			int parentHeight) {
		super.initialize(width, height, parentWidth, parentHeight);
		mCamera = new Camera();
	}
	@Override
	protected void applyTransformation(float interpolatedTime, Transformation t) {
		final float fromDegrees = mFromDegrees;
		float degrees = fromDegrees
				+ ((mToDegrees - fromDegrees) * interpolatedTime);
		final float centerX = mCenterX;
		final float centerY = mCenterY;
		final Camera camera = mCamera;
		final Matrix matrix = t.getMatrix();
		camera.save();
		if (mReverse) {
			camera.translate(0.0f, 0.0f, mDepthZ * interpolatedTime);
		} else {
			camera.translate(0.0f, 0.0f, mDepthZ * (1.0f - interpolatedTime));
		}
		camera.rotateY(degrees);
		camera.getMatrix(matrix);
		camera.restore();
		matrix.preTranslate(-centerX, -centerY);
		matrix.postTranslate(centerX, centerY);
	}
}
```