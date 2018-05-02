实现方式，利用RectF画弧形

```
public class RoundCountDownView extends View {
    private Context context;
    private int foregroundColor; //前景颜色
    private int backgroundColor; //后景颜色
    private int fillColor; //填充颜色
    private float max; //最大值
    private boolean isClockwise; //顺时针or逆时针

    private Paint a;
    private Paint b;
    private Paint c;

    private RectF d;
    private RectF e;

    private int f = 360;
    private int g = -90;

    private float width;

    public RoundCountDownView(Context context) {
        super(context);
        init();
    }

    public RoundCountDownView(Context context, AttributeSet attrs) {
        super(context, attrs);
        initParameters(context, attrs);
        init();
    }

    public RoundCountDownView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initParameters(context, attrs);
        init();
    }

    @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    public RoundCountDownView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
        initParameters(context, attrs);
        init();
    }

    private void initParameters(Context context, AttributeSet attrs) {
        this.context = context;
        TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.RoundCountDownView);

        width = typedArray.getDimension(R.styleable.RoundCountDownView_width,
                getResources().getDimension(R.dimen.round_count_down_view));

        foregroundColor = typedArray.getColor(R.styleable.RoundCountDownView_foregroundColor,
                getResources().getColor(R.color.theme_main_2));

        backgroundColor = typedArray.getColor(R.styleable.RoundCountDownView_backgroundColor,
                getResources().getColor(R.color.white));

        fillColor = typedArray.getColor(R.styleable.RoundCountDownView_fillColor,
                getResources().getColor(R.color.white));

        max = typedArray.getFloat(R.styleable.RoundCountDownView_max, 100.0f);

        isClockwise = typedArray.getBoolean(R.styleable.RoundCountDownView_isClockwise, true);

        Log.d("RoundCountDownView", "max: " + max + " isClockwise: " + isClockwise + " width: " + width);

        /*if (isClockwise) {
            int temp = backgroundColor;
            backgroundColor = foregroundColor;
            foregroundColor = temp;
        }*/
    }

    private void init() {
        this.a = new Paint();
        this.a.setStyle(Paint.Style.STROKE);
        this.a.setStrokeWidth(this.width);
        this.a.setColor(foregroundColor);
        this.a.setAntiAlias(true);
        this.c = new Paint();
        this.c.setStyle(Paint.Style.STROKE);
        this.c.setStrokeWidth(this.width);
        this.c.setColor(backgroundColor);
        this.c.setAntiAlias(true);
        this.b = new Paint();
        this.b.setStyle(Paint.Style.FILL);
        this.b.setStrokeWidth(this.width);
        this.b.setColor(fillColor);
        this.b.setAntiAlias(true);
        this.d = new RectF();
        this.e = new RectF();
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        this.d.set(this.width, this.width, getWidth() - this.width, getHeight() - this.width);
        this.e.set(2 * this.width - 1, 2 * this.width - 1, getWidth() - 2 * this.width + 1, getHeight() - 2 * this.width + 1);
        canvas.drawArc(this.d, 0.0F, 360.0F, true, this.c);
        canvas.drawArc(this.d, this.g, this.f, true, this.a);
        canvas.drawArc(this.e, 0.0F, 360.0F, true, this.b);
    }

    public void setProgress(float paramInt) {
        if (isClockwise)  {
            /*paramInt = max - paramInt;*/
            paramInt = -paramInt;
        }
        this.f = ((int) (360.0F * (paramInt / max)));
        Log.d("RoundCountDownView", "paramInt: " + paramInt + " this.f: " + this.f + " max: " + max);
        postInvalidate();
    }
}
```

attrs.xml
```
	<declare-styleable name="RoundCountDownView">
        <attr name="foregroundColor" format="color" />
        <attr name="backgroundColor" format="color" />
        <attr name="fillColor" format="color" />
        <attr name="width" format="dimension" />
        <attr name="isClockwise" format="boolean" />
        <attr name="max" format="float" />
    </declare-styleable>
```