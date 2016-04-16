## for Android: Custom View | 自定义视图
>步骤
- 定义View所用属性
- 在View构造函数中获得属性值
- 重写onMeasure
- 重写onDraw

### 定义View所用属性
在res/values文件夹下，添加attrs.xml文件
```xml
attrs.xml
<?xml version="1.0" encoding="utf-8"?>
<resources>

    <attr name="titleText" format="string"/>
    <attr name="titleColor" format="color"/>
    <attr name="titleSize" format="dimension"/>

    <declare-styleable name="CustomTitleView">
        <attr name="titleText"/>
        <attr name="titleColor"/>
        <attr name="titleSize"/>
    </declare-styleable>

</resources>
```

*format的类型:* **integer float boolean string color dimension reference enum ...**
### 在View构造函数中获得属性值
```java
public class CustomTitleView extends View {
	
	private String mTitleString;;
	private int mTitleColor;
	private int mTitleSize;
	
	private Paint mPaint;
	private Rect mTitleBound;
	
	public CustomTitleView(Context context){
		this(context, null);
	}
	
	public CustomTitleView(Context context,AttributeSet attrs){
		this(context, attrs, 0);
	}
	
	public CustomTitleView(Context context,AttributeSet attrs,int defStyle){
		super(context,attrs,defStyle);
		
		this.setOnClickListener(new View.OnClickListener() {
			
			@Override
			public void onClick(View v) {
				mTitleString = randomText();
				postInvalidate();
			}
		});
		
		mTitleColor = Color.BLACK;
		TypedArray array = context.getTheme().obtainStyledAttributes(
				attrs, R.styleable.CustomTitleView, defStyle, 0);
		int n = array.getIndexCount();
		for(int i = 0;i<n;i++){
			int attr = array.getIndex(i);
			switch (attr) {
			case R.styleable.CustomTitleView_titleText:
				mTitleString = array.getString(attr);
				break;
			case R.styleable.CustomTitleView_titleColor:
				mTitleColor = array.getColor(attr, Color.BLACK);
				break;
			case R.styleable.CustomTitleView_titleSize:
				mTitleSize = array.getDimensionPixelSize(attr, (int)TypedValue.applyDimension(
						TypedValue.COMPLEX_UNIT_SP, 16, getResources().getDisplayMetrics()));
			default:
				break;
			}
		}
		array.recycle();
		
		mPaint = new Paint();
		mPaint.setTextSize(mTitleSize);
		
		mTitleBound = new Rect();
		mPaint.getTextBounds(mTitleString, 0, mTitleString.length(), mTitleBound);
	}


	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		int widthMode = MeasureSpec.getMode(widthMeasureSpec);
		int widthSize = MeasureSpec.getSize(widthMeasureSpec);
		int heightMode = MeasureSpec.getMode(heightMeasureSpec);
		int heightSize = MeasureSpec.getSize(heightMeasureSpec);
		
		int width;
		int height;
		
		if(widthMode == MeasureSpec.EXACTLY){
			width = widthSize;
		}else{
			mPaint.setTextSize(mTitleSize);
			mPaint.getTextBounds(mTitleString, 0, mTitleString.length(), mTitleBound);
			width= getPaddingLeft() + mTitleBound.width() + getPaddingRight();
		}
		
		if(heightMode == MeasureSpec.EXACTLY){
			height = heightSize;
		}else{
			mPaint.setTextSize(mTitleSize);
			mPaint.getTextBounds(mTitleString, 0, mTitleString.length(), mTitleBound);
			height = getPaddingTop() + mTitleBound.height() + getPaddingBottom();
		}
		setMeasuredDimension(width, height);
	}
	
	@Override
	protected void onDraw(Canvas canvas) {
		Random random = new Random();
		mPaint.setColor(Color.GRAY);
		canvas.drawRect(0, 0, getMeasuredWidth(), getMeasuredHeight(), mPaint);
		
		mPaint.setColor(mTitleColor);
		canvas.drawText(mTitleString, 
				getWidth()/2-mTitleBound.width()/2, 
				getHeight()/2+mTitleBound.height()/2, mPaint);
		
		mPaint.setColor(Color.RED);
		for(int i = 0;i<1000;i++){
			canvas.drawCircle(random.nextInt(getWidth()), random.nextInt(getHeight()), 1, mPaint); 
		}
	}

	private String randomText(){
		Random random = new Random();
		Set<Integer> set = new HashSet<Integer>();
		while(set.size()<4){
			set.add(random.nextInt(10));
		}
		StringBuilder sBuilder = new StringBuilder();
		for(Integer i:set){
			sBuilder.append(""+i);
		}
		return sBuilder.toString();
	}
}
```
