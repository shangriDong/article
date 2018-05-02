转载自：http://www.cnblogs.com/withwind318/p/5541267.html
 感谢作者！！

先解释一个类：Paint.FontMetrics，它表示绘制字体时的度量标准。google的官方api文档对它的字段说明如下：
![这里写图片描述](http://img.blog.csdn.net/20170323165901207?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

ascent: 字体最上端到基线的距离，为负值。
descent：字体最下端到基线的距离，为正值。
如下图：
![这里写图片描述](http://img.blog.csdn.net/20170323170018603?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ3FpdXNoYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

中间那条线就是基线，基线到上面那条线的距离就是ascent，基线到下面那条线的距离就是descent。
 
回到主题，我们要让imagespan与text对齐，只需把imagespan放到descent线和ascent线之间的中间位置就可以了。实现方式为重写ImageSpan类的draw方法。最终实现方法如下：

```
@Override
public void draw(@NonNull Canvas canvas, CharSequence text,
                 int start, int end, float x,
                 int top, int y, int bottom, @NonNull Paint paint) {
     // image to draw
    Drawable b = getDrawable();
    // font metrics of text to be replaced
    Paint.FontMetricsInt fm = paint.getFontMetricsInt();
    int transY = (y + fm.descent + y + fm.ascent) / 2 
            - b.getBounds().bottom / 2;
    
    canvas.save();
    canvas.translate(x, transY);
    b.draw(canvas);
    canvas.restore();
}
```

解释下形参：
x，要绘制的image的左边框到textview左边框的距离。
y，要替换的文字的基线坐标，即基线到textview上边框的距离。
top，替换行的最顶部位置。
bottom，替换行的最底部位置。注意，textview中两行之间的行间距是属于上一行的，所以这里bottom是指行间隔的底部位置。
paint，画笔，包含了要绘制字体的度量信息。
这几个参数含义在代码中找不到说明，写了个demo测出来的。top和bottom参数只是解释下，函数里面用不上。
然后解释下代码逻辑：
getDrawable获取要绘制的image，getBounds是获取包裹image的矩形框尺寸；
y + fm.descent得到字体的descent线坐标；
y + fm.ascent得到字体的ascent线坐标；
两者相加除以2就是两条线中线的坐标；
b.getBounds().bottom是image的高度（试想把image放到原点），除以2即高度一半；
前面得到的中线坐标减image高度的一半就是image顶部要绘制的目标位置；
最后把目标坐标传递给canvas.translate函数就可以了，至于这个函数的理解先不管了。
原理上大致就这样了，最后提供本文提出问题的最终解决方案，使用自定义的ImageSpan类，只需重写它的draw函数，代码如下：

```
public class CenteredImageSpan extends ImageSpan {
    
    public CenteredImageSpan(Context context, final int drawableRes) {
        super(context, drawableRes);
    }

    @Override
    public void draw(@NonNull Canvas canvas, CharSequence text,
                     int start, int end, float x,
                     int top, int y, int bottom, @NonNull Paint paint) {
        // image to draw
        Drawable b = getDrawable();
        // font metrics of text to be replaced
        Paint.FontMetricsInt fm = paint.getFontMetricsInt();
        int transY = (y + fm.descent + y + fm.ascent) / 2
                - b.getBounds().bottom / 2;

        canvas.save();
        canvas.translate(x, transY);
        b.draw(canvas);
        canvas.restore();
    }
}
```

释放终极版本：

上面版本的ImageSpan有的手机或者换行会有效果差异，现在送上终极版本：

```
public class VerticalImageSpan extends ImageSpan {

    public VerticalImageSpan(Drawable drawable) {
        super(drawable);
    }

    /**
     * update the text line height
     */
    @Override
    public int getSize(Paint paint, CharSequence text, int start, int end,
                       Paint.FontMetricsInt fontMetricsInt) {
        Drawable drawable = getDrawable();
        Rect rect = drawable.getBounds();
        if (fontMetricsInt != null) {
            Paint.FontMetricsInt fmPaint = paint.getFontMetricsInt();
            int fontHeight = fmPaint.descent - fmPaint.ascent;
            int drHeight = rect.bottom - rect.top;
            int centerY = fmPaint.ascent + fontHeight / 2;

            fontMetricsInt.ascent = centerY - drHeight / 2;
            fontMetricsInt.top = fontMetricsInt.ascent;
            fontMetricsInt.bottom = centerY + drHeight / 2;
            fontMetricsInt.descent = fontMetricsInt.bottom;
        }
        return rect.right;
    }

    /**
     * see detail message in android.text.TextLine
     *
     * @param canvas the canvas, can be null if not rendering
     * @param text the text to be draw
     * @param start the text start position
     * @param end the text end position
     * @param x the edge of the replacement closest to the leading margin
     * @param top the top of the line
     * @param y the baseline
     * @param bottom the bottom of the line
     * @param paint the work paint
     */
    @Override
    public void draw(Canvas canvas, CharSequence text, int start, int end,
                     float x, int top, int y, int bottom, Paint paint) {

        Drawable drawable = getDrawable();
        canvas.save();
        Paint.FontMetricsInt fmPaint = paint.getFontMetricsInt();
        int fontHeight = fmPaint.descent - fmPaint.ascent;
        int centerY = y + fmPaint.descent - fontHeight / 2;
        int transY = centerY - (drawable.getBounds().bottom - drawable.getBounds().top) / 2;
        canvas.translate(x, transY);
        drawable.draw(canvas);
        canvas.restore();
    }
}
```

感谢博主！！！原文链接：http://www.cnblogs.com/withwind318/p/5541267.html