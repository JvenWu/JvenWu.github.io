## for Android: Event Dispatch - View | 的事件分发 - View
> 知识点
- 事件分发流程
- dispatchTouchEvent分析
- setOnTouchListener中的onTouch分析
- onTouchEvent分析

#### 事件分发流程
> 当view触碰时执行顺序为  
1.dispatchTouchEvent  
2.view.setOnTouchListener中的onTouch  
3.onTouchEvent  

打印示例，用于根踪事件的分发：
```java
//自定控件
public class MyBtn extends View {

    private String TAG = "MyBtn";
    public MyBtn(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    public boolean dispatchTouchEvent(MotionEvent event) {
        int action = event.getAction();
        switch (action)
        {
            case MotionEvent.ACTION_DOWN:
                Log.d(TAG, "dispatchTouchEvent ACTION_DOWN");
                break;
            case MotionEvent.ACTION_MOVE:
                Log.d(TAG, "dispatchTouchEvent ACTION_MOVE");
                break;
            case MotionEvent.ACTION_UP:
                Log.d(TAG, "dispatchTouchEvent ACTION_UP");
                break;
            default:
                break;
        }
        return super.dispatchTouchEvent(event);
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int action = event.getAction();
        switch (action)
        {
            case MotionEvent.ACTION_DOWN:
                Log.d(TAG, "onTouchEvent ACTION_DOWN");
                break;
            case MotionEvent.ACTION_MOVE:
                Log.d(TAG, "onTouchEvent ACTION_MOVE");
                break;
            case MotionEvent.ACTION_UP:
                Log.d(TAG, "onTouchEvent ACTION_UP");
                break;
            default:
                break;
        }
        return super.onTouchEvent(event);
    }
}
//MainActivity中
MyBtn btn = (MyBtn)findViewById(R.id.btn);
btn.setOnTouchListener(new View.OnTouchListener() {
    @Override
    public boolean onTouch(View v, MotionEvent event) {
        int action = event.getAction();
        switch (action)
        {
            case MotionEvent.ACTION_DOWN:
                Log.d(TAG, "OnTouchListener.onTouch ACTION_DOWN");
                break;
            case MotionEvent.ACTION_MOVE:
                Log.d(TAG, "OnTouchListener.onTouch ACTION_MOVE");
                break;
            case MotionEvent.ACTION_UP:
                Log.d(TAG, "OnTouchListener.onTouch ACTION_UP");
                break;
            default:
                break;
        }
        return false;
    }
});
```

#### dispatchTouchEvent分析
```java
public boolean dispatchTouchEvent(MotionEvent event) {
    ...
    if (li != null && li.mOnTouchListener != null
        && (mViewFlags & ENABLED_MASK) == ENABLED
        && li.mOnTouchListener.onTouch(this, event)) {
        result = true;
    }
    if (!result && onTouchEvent(event)) {
        result = true;
    }
    ...
}
```

先判断mOnTouchListener不为null，并且view是enable的状态，并且 mOnTouchListener.onTouch(this, event)返回true，
若这三个条件都满足，就设置 result = true，即后面的onTouchEvent(event）不执行

#### setOnTouchListener中的onTouch分析
若设置了View的setOnTouchListener，并且return true，那么View自己的onTouchEvent就不会执行

#### onTouchEvent分析
```java
public boolean onTouchEvent(MotionEvent event) {
    ...
    // Only perform take click actions if we were in the pressed state
    if (!focusTaken) {
        // Use a Runnable and post this rather than calling
        // performClick directly. This lets other visual state
        // of the view update before click actions start.
        if (mPerformClick == null) {
            mPerformClick = new PerformClick();
        }
        if (!post(mPerformClick)) {
            performClick();
        }
    }
    ...
}
```

performClick()调用了mOnClickListener.onClick()，即view的点击事件会在onTouchEvent()中执行
