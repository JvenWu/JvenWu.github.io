## for Android: Animate | 动画
```java
帧动画:
//定义xml动画文件
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
                android:oneshot="false">
    <item android:drawable="@drawable/photo1"
        android:duration="200"/>
    <item android:drawable="@drawable/photo2"
        android:duration="200"/>
</animation-list>

//设置动画播放
ImageView mSwitchPhoto = findViewById(R.id.switch_iv)
mSwitchPhoto.setBackgroundResource(R.drawable.switch_photo);
AnimationDrawable ad = (AnimationDrawable) mSwitchPhoto.getBackground();
ad.start();

补间动画:
//位移动画
/*
参数10指的是X的起点坐标，但不是指屏幕x坐标为10的位置，而是imageview的 原来位置 + 10
参数150指的是X的终点坐标，它的值是imageview的 原来位置 + 150
TranslateAnimation ta = new TranslateAnimation(10, 150, 20, 140);
*/
/*
x坐标的起点位置，如果相对于自己，传0.5f，那么起点坐标就是 原来位置 + 0.5 * view宽度
x坐标的终点位置，如果传入2，那么终点坐标就是 原来位置 + 2 * view的宽度
y坐标的起点位置，如果传入0.5f，那么起点坐标就是 原来位置 + 0.5 * view高度
y坐标的终点位置，如果传入2，那么终点坐标就是 原来位置 + 2 * view高度
*/
TranslateAnimation ta = new TranslateAnimation(
    Animation.RELATIVE_TO_SELF, 0.5f,
    Animation.RELATIVE_TO_SELF, 2,
    Animation.RELATIVE_TO_SELF, 0.5f,
    Animation.RELATIVE_TO_SELF, 2);
ta.setDuration(2000);
ta.setRepeatCount(1);
ta.setRepeatMode(Animation.REVERSE);
ta.setFillAfter(false); //当设置为true时动画结束后不返回原点
mSwitchPhoto.startAnimation(ta);

//旋转动画
/*
pivotX 为距离左侧的偏移量, pivotY 为距离顶部的偏移量
RotateAnimation rs = new RotateAnimation(0,360,100,100);
*/
/*
pivotXValue 传0.5时, 原来左侧位置 + 0.5 * view宽度
pivotYValue 传0.5时, 原来顶部位置 + 0.5 * view宽度
*/
RotateAnimation ra = new RotateAnimation(
    0, 720, 
    Animation.RELATIVE_TO_SELF, 0.5f,
    Animation.RELATIVE_TO_SELF, 0.5f);
ra.setDuration(2000);
ra.setRepeatCount(1);
ra.setRepeatMode(Animation.REVERSE);
mRotatePhoto.startAnimation(ra);

//缩放动画
/*
o.5f表示动画的起始宽度是真实宽度的0.5倍
2表示动画结束宽度是真实宽度的2倍
o.1f表示动画的起始宽度是真实高度的0.1倍
3表示动画结束宽度是真实高度的3倍
后面参数表示缩放中心点的位置 默认是左上角
*/
ScaleAnimation sa = new ScaleAnimation(0.5f, 2, 0.1f, 3, Animation.RELATIVE_TO_SELF, 0.8f,
                Animation.RELATIVE_TO_SELF, 0.5f);
sa.setDuration(2000);
sa.setRepeatCount(1);
sa.setRepeatMode(Animation.REVERSE);
sa.setFillAfter(true);
mRotatePhoto.startAnimation(sa);

//透明动画
//0表示完全透明 1表示不透明
aa = new AlphaAnimation(0, 1);
aa.setDuration(2000);
sa.setRepeatCount(1);
mRotatePhoto.startAnimation(aa);

//动画组
//false 为使用默认的动画插补器
AnimationSet set = new AnimationSet(false);
set.addAnimation(ta);
set.addAnimation(sa);
set.addAnimation(ra);
set.addAnimation(aa);
mRotatePhoto.startAnimation(set);  

//通过加载xml实现(动画文件在res/anim)
//xml定义动画
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <alpha
        android:duration="500"
        android:fromAlpha="0.0"
        android:toAlpha="1.0" />
    <translate
        android:duration="500"
        android:fillAfter="false"
        android:fromXDelta="150" <!-- 相对于原来位置的偏移 -->
        android:toXDelta="0" />

</set>
//从xml文件加载动画
Animation anim1 = AnimationUtils.loadAnimation(getActivity(),R.anim.guide1_faxian_anim);
myView.setAnimation(anim1);

属性动画:
//位移
/*
第一个参数target指定要显示动画的组件
第二个参数propertyName指定要改变组件的哪个属性
第三个参数values是可变参数，就是属性的值, 可传多个值连续改变
*/
ObjectAnimator oa = ObjectAnimator.ofFloat(mRotatePhoto, "translationX", 100);
oa.setDuration(2000);
oa.setRepeatCount(1);
oa.setRepeatMode(ValueAnimator.REVERSE);
oa.start();

//缩放
ObjectAnimator oa = ObjectAnimator.ofFloat(mRotatePhoto, "scaleX", 1, 1.6f, 1.2f, 2);
oa.setDuration(2000);
oa.start();

//透明
ObjectAnimator oa = ObjectAnimator.ofFloat(iv, "alpha", 0, 0.5f, 0.2f, 1);
oa.setDuration(2000);
oa.start();

//翻转
/*
属性指定为rotationX是竖直翻转
属性指定为rotationY是水平翻转
rotation指定是顺时针旋转
*/
ObjectAnimator oa = ObjectAnimator.ofFloat(mRotatePhoto, "rotationY", 0, 180, 90, 360);
oa.setDuration(2000);
oa.setRepeatCount(1);
oa.setRepeatMode(ValueAnimator.REVERSE);
oa.start();

//动画组
AnimatorSet set = new AnimatorSet();
set.setTarget(mRotatePhoto);
//set.playSequentially(oa, oa2, oa3);//所有动画有先后顺序的播放
set.playTogether(oa, oa1, oa2, oa3);//所有动画一起播放
set.start();

加载xml属性动画:
//定义xml动画
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <objectAnimator
        android:propertyName="translationX"
        android:duration="2000"
        android:repeatCount="1"
        android:repeatMode="reverse"
        android:valueFrom="-100"
        android:valueTo="100"
        >
    </objectAnimator>
</set>
//加载动画
Animator at = AnimatorInflater.loadAnimator(this, R.animator.objanimator);
at.setTarget(mRotatePhoto);
at.start();


```
