效果图：
![这里写图片描述](http://img.blog.csdn.net/20170316095104154?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

java代码
```
public class SeekBarRelativeLayout extends RelativeLayout {
    public SeekBarRelativeLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public SeekBarRelativeLayout(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    private TextView textView;
    private SeekBar seekBar;
    private SeekBar.OnSeekBarChangeListener onSeekBarChangeListener;
    private int textViewPaddingLeft = 0;

    private void setText(String str) {
        textView.setText(str);
    }

    private void setMarginLeftForTextView(int progress) {
        if (seekBar != null && textView != null) {
            LayoutParams layoutParams = (LayoutParams) textView.getLayoutParams();
            int width = seekBar.getWidth() - seekBar.getPaddingLeft() - seekBar.getPaddingRight();
            layoutParams.leftMargin = (int) (((float) progress / seekBar.getMax()) * width);

            layoutParams.leftMargin += seekBar.getPaddingRight() - textView.getWidth() / 2 + textViewPaddingLeft;

            setText(Integer.toString(progress));
            textView.setLayoutParams(layoutParams);
        }
    }

    public void setProgress(int process) {
        if (seekBar != null) {
            seekBar.setProgress(process);
        }
    }

    public void setEnabled(boolean enabled) {
        if (seekBar != null) {
            seekBar.setEnabled(enabled);
        }
    }

    public void initSeekBar() {
        seekBar = (SeekBar) findViewById(R.id.seek_bar_relative_layout_seek_bar);
        textView = (TextView) findViewById(R.id.seek_bar_relative_layout_text_view);

        textView.setVisibility(INVISIBLE);

        textViewPaddingLeft = ((LayoutParams) textView.getLayoutParams()).leftMargin;
        if (seekBar != null && textView != null) {
            seekBar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
                @Override
                public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                    setMarginLeftForTextView(progress);
                    if (onSeekBarChangeListener != null) {
                        onSeekBarChangeListener.onProgressChanged(seekBar, progress, fromUser);
                    }
                }

                @Override
                public void onStartTrackingTouch(SeekBar seekBar) {
                    if (onSeekBarChangeListener != null) {
                        onSeekBarChangeListener.onStartTrackingTouch(seekBar);
                    }

                    textView.setVisibility(VISIBLE);
                }

                @Override
                public void onStopTrackingTouch(SeekBar seekBar) {
                    if (onSeekBarChangeListener != null) {
                        onSeekBarChangeListener.onStopTrackingTouch(seekBar);
                    }
                    textView.setVisibility(INVISIBLE);
                }
            });
        }
    }

    public void setOnSeekBarChangeListener(SeekBar.OnSeekBarChangeListener onSeekBarChangeListener) {
        this.onSeekBarChangeListener = onSeekBarChangeListener;
    }
}
```

布局文件
```
<?xml version="1.0" encoding="utf-8"?>
<SeekBarRelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="44dp"
    android:paddingLeft="30dp"
    android:paddingRight="30dp">

    <TextView
        android:id="@id/seek_bar_relative_layout_describe"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="17.5dp"
        android:text="强度"
        android:textColor="@color/#909896"
        android:textSize="13sp" />

    <TextView
        android:id="@id/seek_bar_relative_layout_text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="24dp"
        android:text="0"
        android:textColor="@color/#909896"
        android:textSize="13sp" />

    <SeekBar
        android:id="@id/seek_bar_relative_layout_seek_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_marginLeft="25dp"
        android:background="@null"
        android:max="100"
        android:maxHeight="3px"
        android:minHeight="3px"
        android:progress="100"
        android:progressDrawable="@drawable/beauty_face_seek_bar"
        android:splitTrack="false"
        android:thumb="@drawable/beauty_thumb" />

</SeekBarRelativeLayout>
```

实例化后，调用`initSeekBar();`进行初始化

github 源码：

```
https://github.com/shangriDong/SeekBar.git
```