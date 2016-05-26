## for Android: Broadcast | 广播的使用
```java
//定义广播接收类
class ProjectUpdateBroadcastReceive extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
		String actionStr = intent.getAction();
		if (TaskModifyActivity.ACTION_CREATE_TASK.equals(actionStr)) {
			//TODU
			//mPtrFrameLayout.autoRefresh(false);
		}
	}
}
```
