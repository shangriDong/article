```
package com.shangri.utils;

import android.content.Context;
import android.hardware.SensorManager;
import android.view.OrientationEventListener;

/**
 * Created by Shangri on 2016/10/21.
 */

public class SensorManagerUtil {
    private int mOrientation;
    private AlbumOrientationEventListener mAlbumOrientationEventListener;
    private static SensorManagerUtil instance;
    private SensorManagerUtil() {

    }

    public static SensorManagerUtil getInstance() {
        if (instance == null) {
            instance = new SensorManagerUtil();
        }
        return instance;
    }

    public void initAlbumOrientationEventListener(Context context, RotateListener rotateListener) {
        if (mAlbumOrientationEventListener != null) {
            mAlbumOrientationEventListener.disable();
            mAlbumOrientationEventListener = null;
        }
        mAlbumOrientationEventListener = new AlbumOrientationEventListener(context, SensorManager.SENSOR_DELAY_NORMAL, rotateListener);
        if (mAlbumOrientationEventListener.canDetectOrientation()) {
            mAlbumOrientationEventListener.enable();
        } else {
            //Log.d("chengcj1", "Can't Detect Orientation");
        }
    }

    public void disableOrientationEventListener() {
        if (mAlbumOrientationEventListener != null) {
            mAlbumOrientationEventListener.disable();
        }
        mAlbumOrientationEventListener = null;
    }

    private class AlbumOrientationEventListener extends OrientationEventListener {

        private  RotateListener rotateListener;

        public AlbumOrientationEventListener(Context context) {
            super(context);
        }

        public AlbumOrientationEventListener(Context context, int rate) {
            super(context, rate);
        }

        public AlbumOrientationEventListener(Context context, int rate, RotateListener rotateListener) {
            super(context, rate);
            this.rotateListener = rotateListener;
        }

        @Override
        public void onOrientationChanged(int orientation) {
            if (orientation == OrientationEventListener.ORIENTATION_UNKNOWN) {
                return;
            }

            //保证只返回四个方向
            int newOrientation = ((orientation + 45) / 90 * 90) % 360;
            if (newOrientation != mOrientation) {
                mOrientation = newOrientation;

                //返回的mOrientation就是手机方向，为0°、90°、180°和270°中的一个
                if (rotateListener != null) {
                    rotateListener.changed(mOrientation);
                }
            }
        }
    }

    public interface RotateListener {
        void changed(int orientation);
    }
}

```