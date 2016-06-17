## for Android: Activity Lifecycle | Activity 生命周期

> Activity 有如下生命周期函数
- onCreate
- onStart
- onResume
- onPause
- onStop
- onDestroy
- onRestart

如当Activity被其它Acivity覆盖时就处于不可见状态；当Activity中弹出dialog，
这时Activity仍可见，但是没有焦点，不可交互，就处于非活动状态

#### 常见情况的生命周期
- 一般生命周期为 onCreate -> onStart -> onResume(活动状态) -> onPause(非活动状态) -> onStop(不可见) -> onDestroy(销毁)
- 非活动转活动为 onPause -> onResume  
- 活动转不可见为 onResume -> onPause -> onStop  
- 不可见转活动为 onStop -> onRestart -> onStart -> onResume

处于onPause或onStop 的Activity都可能由于内存不足而被系统回收掉，这时直接回收掉，不走onDestroy函数
