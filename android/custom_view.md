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
	//文本信息
	private String mTitleString;
	//文字颜色
	private int mTitleColor;
	//文字大小
	private int mTitleSize;
	
	//画笔
	private Paint mPaint;
	//文字所占距形
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
		
		//设置默认字体颜色
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
				//设置字体大小，默认为16sp，要把sp转换成px
				mTitleSize = array.getDimensionPixelSize(attr, (int)TypedValue.applyDimension(
						TypedValue.COMPLEX_UNIT_SP, 16, getResources().getDisplayMetrics()));
			default:
				break;
			}
		}
		//回收资源
		array.recycle();
		
		mPaint = new Paint();
		mPaint.setTextSize(mTitleSize);
		
		//获取文字所占距形大小
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
		//绘制底框矩形背景
		mPaint.setColor(Color.GRAY);
		canvas.drawRect(0, 0, getMeasuredWidth(), getMeasuredHeight(), mPaint);
		
		//绘制文本
		mPaint.setColor(mTitleColor);
		canvas.drawText(mTitleString, 
				getWidth()/2-mTitleBound.width()/2, 
				getHeight()/2+mTitleBound.height()/2, mPaint);
		//绘制噪点
		mPaint.setColor(Color.RED);
		for(int i = 0;i<1000;i++){
			canvas.drawCircle(random.nextInt(getWidth()), random.nextInt(getHeight()), 1, mPaint); 
		}
	}
	//生成4位随机数
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
### 布局文件中使用
```xml
<!-- 注意引入自定义View所在命名空间 xmlns:custom="http://schemas.android.com/apk/res/com.zkbr.demo" -->
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:custom="http://schemas.android.com/apk/res/com.zkbr.demo"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity" >
	
    <com.zkbr.demo.CustomTitleView 
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        custom:titleText="hello world"
        custom:titleColor="#00ff00"
        custom:titleSize="40dp"
        android:layout_centerInParent="true"  />

</RelativeLayout>
```
>测量模式
- MeasureSpec.EXACTLY  
- MeasureSpec.AT_MOST  
- MeasureSpec.UISPECIFIED

>

### Sample Below
```xml
attrs.xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <attr name="titleText" format="string"/>
    <attr name="titleColor" format="color"/>
    <attr name="titleSelectedColor" format="color"/>
    <attr name="titleSize" format="dimension"/>
    <attr name="image" format="reference"/>
    <attr name="selectedImage" format="reference"/>
    <attr name="imageScaleType">
        <enum name="fillXY" value="0"/>
        <enum name="center" value="1"/>
    </attr>
    
    <declare-styleable name="TitleImageMenu">
        <attr name="titleText"/>
	    <attr name="titleColor"/>
	    <attr name="titleSelectedColor"/>
	    <attr name="titleSize"/>
	    <attr name="image"/>
	    <attr name="selectedImage"/>
	    <attr name="imageScaleType"/>
    </declare-styleable>
</resources>
```

```java
TitleImageMenu.java
public class TitleImageMenu extends View {
	private String mTitleText;
	private int mTitleColor;
	private int mTitleSelectedColor;
	private int defColor;
	private int mTitleSize;
	private Bitmap mImage;
	private Bitmap mSelectedImage;
	private Bitmap defImage;
	private int mImageScaleType;
	
	private Rect mImgRect;
	private Rect mTxtBounds;
	private Paint mPaint;
	
	private int mWidth;
	private int mHeight;
	private boolean isSelected = true;
	public TitleImageMenu(Context context){
		this(context, null);
	}
	public TitleImageMenu(Context context,AttributeSet attrs){
		this(context,attrs,0);
	}
	public TitleImageMenu(Context context,AttributeSet attrs,int defStyle){
		super(context, attrs, defStyle);
		
		TypedArray array = context.getTheme().obtainStyledAttributes(
				attrs, R.styleable.TitleImageMenu, defStyle, 0);
		int n = array.getIndexCount();
		for(int i = 0;i<n;i++){
			int attr = array.getIndex(i);
			switch (attr) {
			case R.styleable.TitleImageMenu_titleText:
				mTitleText = array.getString(attr);
				break;
			case R.styleable.TitleImageMenu_titleColor:
				mTitleColor = array.getColor(attr, Color.BLACK);
				break;
			case R.styleable.TitleImageMenu_titleSelectedColor:
				mTitleSelectedColor = array.getColor(attr, Color.BLACK);
				break;
			case R.styleable.TitleImageMenu_titleSize:
				mTitleSize = array.getDimensionPixelSize(attr, (int)TypedValue.applyDimension(
						TypedValue.COMPLEX_UNIT_SP, 16, getResources().getDisplayMetrics()));
				break;
			case R.styleable.TitleImageMenu_image:
				mImage = BitmapFactory.decodeResource(getResources(), array.getResourceId(attr, 0));
				break;
			case R.styleable.TitleImageMenu_selectedImage:
				mSelectedImage = BitmapFactory.decodeResource(getResources(), array.getResourceId(attr, 0));
				break;
			case R.styleable.TitleImageMenu_imageScaleType:
				mImageScaleType = array.getInt(attr, 0);
				break;
			default:
				break;
			}
		}
		array.recycle();
		
		mImgRect = new Rect();
		mTxtBounds = new Rect();
		mPaint = new Paint();
		mPaint.setTextSize(mTitleSize);
		mPaint.getTextBounds(mTitleText, 0, mTitleText.length(), mTxtBounds);
		defImage = mImage;
		defColor = mTitleColor;
		
		this.setOnClickListener(new OnClickListener() {
			
			@Override
			public void onClick(View v) {
				if(isSelected){
					defColor = mTitleSelectedColor;
					defImage = mSelectedImage;
				}else{
					defColor = mTitleColor;
					defImage = mImage;
				}
				isSelected = !isSelected;
				postInvalidate();
			}
		});
	}
	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		//绘制边框
		mPaint.setStyle(Paint.Style.STROKE);
		mPaint.setColor(Color.RED);
		mPaint.setStrokeWidth(3);
		canvas.drawRect(0, 0, getMeasuredWidth(), getMeasuredHeight(), mPaint);
		
		//绘制文字
		mPaint.setStyle(Paint.Style.FILL);
		mPaint.setColor(defColor);
		if(mWidth<mTxtBounds.width()){
			TextPaint paint = new TextPaint(mPaint);
			String string = TextUtils.ellipsize(mTitleText, paint, 
					mWidth - getPaddingLeft() - getPaddingRight(), 
					TextUtils.TruncateAt.END).toString();
			canvas.drawText(string, 
					mWidth - getPaddingLeft(), mHeight - getPaddingBottom(), mPaint);
		}else{
			canvas.drawText(mTitleText, 
					mWidth/2 - mTxtBounds.width()/2, 
					mHeight - getPaddingBottom(), mPaint);
		}
		
		mImgRect.left = getPaddingLeft();
		mImgRect.top = getPaddingTop();
		mImgRect.right = mWidth - getPaddingRight();
		mImgRect.bottom = mHeight - getPaddingBottom() - mTxtBounds.height();
		
		//绘制图片
		if(mImageScaleType == 0){
			canvas.drawBitmap(defImage, null, mImgRect,mPaint);
		}else{
			mImgRect.left = mWidth/2 - defImage.getWidth()/2;
			mImgRect.top = (mHeight - getPaddingBottom() - mTxtBounds.height() + getPaddingTop())/2 - defImage.getHeight()/2;
			mImgRect.right = mWidth/2 + defImage.getWidth()/2;
			mImgRect.bottom = (mHeight - getPaddingBottom() - mTxtBounds.height() + getPaddingTop())/2 + defImage.getHeight()/2;
			canvas.drawBitmap(defImage, null, mImgRect,mPaint);
		}
		
	}
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		super.onMeasure(widthMeasureSpec, heightMeasureSpec);
		int specMode = MeasureSpec.getMode(widthMeasureSpec);
		int specSize = MeasureSpec.getSize(widthMeasureSpec);
		
		//测量宽度
		if(specMode == MeasureSpec.EXACTLY){ //match_parent accuracy
			mWidth = specSize;
		}else if(specMode == MeasureSpec.AT_MOST){
			int byImgWidth = getPaddingLeft() + getPaddingRight() + 
					mImage.getWidth()>mSelectedImage.getWidth()?mImage.getWidth():mSelectedImage.getWidth();
			int byTxtWidth = getPaddingLeft() + getPaddingRight() + mTxtBounds.width();
			
			mWidth = Math.max(byImgWidth, byTxtWidth);
		}
		
		//测量高度
		specMode = MeasureSpec.getMode(heightMeasureSpec);
		specSize = MeasureSpec.getSize(heightMeasureSpec);
		if(specMode == MeasureSpec.EXACTLY){
			mHeight = specSize;
		}else if(specMode == MeasureSpec.AT_MOST){
			mHeight = getPaddingTop() + getPaddingBottom() + 
					mImage.getHeight()>mSelectedImage.getHeight()?mImage.getHeight():mSelectedImage.getHeight() 
					+ mTxtBounds.height();
		}
		setMeasuredDimension(mWidth, mHeight);
	}
}
```
