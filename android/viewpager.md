```java
//定义PagerAdapter适配器类
private class MyPagerAdapter extends FragmentStatePagerAdapter {
        public ScreenSlidePagerAdapter(FragmentManager fm) {
            super(fm);
        }

        @Override
        public Fragment getItem(int position) {
            MyFragment fm = null;
            switch(position){
                case 0:
                    break;
                case 1:
                    break;
            }
            return fm;
        }

        @Override
        public int getCount() {
            return NUM_PAGES;
        }
}

//定义viewpager切换动画类
//position可能的值 [-Infinity,-1), [-1,0], (0,1], [1,Infinity]
/*
a 是第一页
b 是第二页
当前页为 a, 当 a 向左滑动,直到滑到 b 时:
a 的position变化是  [-1 ,   0]   由  0  慢慢变到 -1
b 的position变化是  ( 0 ,   1]   由  1  慢慢变到  0

当前页为b,  当 b 向右滑动, 直到滑到a 时:
a 的position变化是  [-1 ,   0]   由  -1  慢慢变到 0
b 的position变化是  ( 0 ,   1]   由   0   慢慢变到 1
*/
public class ZoomOutPageTransformer implements PageTransformer {
    private static float MIN_SCALE = 0.85f;

    private static float MIN_ALPHA = 0.5f;

    @Override
    public void transformPage(View view, float position) {
        int pageWidth = view.getWidth();
        int pageHeight = view.getHeight();

        if (position < -1) { // [-Infinity,-1)
                                // This page is way off-screen to the left.
            view.setAlpha(0);
        } else if (position <= 1) { // [-1,1]
                                    // Modify the default slide transition to
                                    // shrink the page as well
            float scaleFactor = Math.max(MIN_SCALE, 1 - Math.abs(position));
            float vertMargin = pageHeight * (1 - scaleFactor) / 2;
            float horzMargin = pageWidth * (1 - scaleFactor) / 2;
            if (position < 0) {
                view.setTranslationX(horzMargin - vertMargin / 2);
            } else {
                view.setTranslationX(-horzMargin + vertMargin / 2);
            }
            // Scale the page down (between MIN_SCALE and 1)
            view.setScaleX(scaleFactor);
            view.setScaleY(scaleFactor);
            // Fade the page relative to its size.
            view.setAlpha(MIN_ALPHA + (scaleFactor - MIN_SCALE)
                    / (1 - MIN_SCALE) * (1 - MIN_ALPHA));
        } else { // (1,+Infinity]
                    // This page is way off-screen to the right.
            view.setAlpha(0);
        }
    }
}

//设置viewPager与adapter关联，及切换动画
MyPagerAdapter pagerAdapter = new MyPagerAdapter(getSupportFragmentManager());
mViewPager.setAdapter(pagerAdapter);
mViewPager.setPageTransformer(true, new ZoomOutPageTransformer());

//设置view切换回调
mViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
    @Override
    public void onPageScrolled(int i, float v, int i1) {

    }

    @Override
    public void onPageSelected(int i) {

    }

    @Override
    public void onPageScrollStateChanged(int i) {

    }
});

```
