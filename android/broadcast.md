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

//注册广播
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction(TaskModifyActivity.ACTION_CREATE_TASK);
updateReceive = new ProjectUpdateBroadcastReceive();
getActivity().registerReceiver(updateReceive, intentFilter);

//发送广播
Intent intentBroadCast = new Intent();
intentBroadCast.setAction(TaskModifyActivity.ACTION_CREATE_TASK);
sendBroadcast(intentBroadCast);
```
