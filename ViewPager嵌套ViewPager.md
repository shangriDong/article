两个ViewPager嵌套，实现无限循环即：A(1)-A2(B1)-A2(B2)-A(1)
A：父ViewPager 有2个选项，B:为子ViewPager.同样有2个选项  A2即为B.

如果子选项为图片即可以才用较简单办法做到无限循环：
http://blog.csdn.net/chenguang79/article/details/49486599 可参考该博客。

本文介绍的是子选项为fragment,且嵌套有两层ViewPager。

大家可以试一下 简单的嵌套两层viewpager之后，子和父是不能顺利切换的，想要解决这个问题要引入第一份代码ChildViewPager

```
import android.content.Context;
import android.support.v4.view.ViewPager;
import android.util.AttributeSet;
import android.view.MotionEvent;

/**
 * Created by Shangri on 2017/10/10.
 */

public class ChildViewPager extends ViewPager {

    public ChildViewPager(Context context, AttributeSet attrs) {
        super(context, attrs);
        //TODO Auto-generated constructor stub
    }

    private ViewPager mPager;
    private int abc = 1;
    private float mLastMotionX;

    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        if (mPager != null) {
            final float x = ev.getX();
            switch (ev.getAction()) {
                case MotionEvent.ACTION_DOWN:
                    mPager.requestDisallowInterceptTouchEvent(true);
                    abc = 1;
                    mLastMotionX = x;
                    break;
                case MotionEvent.ACTION_MOVE:
                    if (abc == 1) {
                        if (x - mLastMotionX > 5 && getCurrentItem() == 0) {
                            abc = 0;
                            mPager.requestDisallowInterceptTouchEvent(false);
                        }


                        if (x - mLastMotionX < -5 && getCurrentItem() == getAdapter().getCount() - 1) {
                            abc = 0;
                            mPager.requestDisallowInterceptTouchEvent(false);
                        }
                    }
                    break;
                case MotionEvent.ACTION_UP:
                case MotionEvent.ACTION_CANCEL:
                    mPager.requestDisallowInterceptTouchEvent(false);
                    break;
            }
        }
        return super.dispatchTouchEvent(ev);
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        // TODO Auto-generated method stub
        return super.onInterceptTouchEvent(ev);
    }


    @Override
    public boolean onTouchEvent(MotionEvent event) {
        // TODO Auto-generated method stub
        return super.onTouchEvent(event);
    }

    public void setParentPager(ViewPager viewPager) {
        this.mPager = viewPager;
    }
}

```
其中重点是onTouch时间的传递，可参考博客http://blog.csdn.net/dongqiushan/article/details/64457189

其中还涉及到一个知识点就是：

```
mPager.requestDisallowInterceptTouchEvent(false);
```
http://blog.csdn.net/chaihuasong/article/details/17499799 可参考该博客介绍相当好。

简单说就是通知 父布局是否处理该事件。当子ViewPage滑到第1个元素并继续左滑，就通知父ViewPager处理该事件。

至此已经能在子父ViewPager间进行切换，但当父ViewPager在第一个元素继续左滑时 还不能切换。

未完待续！！！！