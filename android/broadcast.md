## for Android: Broadcast | 广播的使用
```java
//定义广播接收类
class MyBroadcastReceive extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
		String actionStr = intent.getAction();
		if (MyActivity.ACTION_CREATE_TASK.equals(actionStr)) {
			//TODU
			//mPtrFrameLayout.autoRefresh(false);
		}
	}
}

//注册广播
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction(MyActivity.ACTION_CREATE_TASK);
updateReceive = new MyBroadcastReceive();
getActivity().registerReceiver(updateReceive, intentFilter);

//发送广播
Intent intentBroadCast = new Intent();
intentBroadCast.setAction(MyActivity.ACTION_CREATE_TASK);
sendBroadcast(intentBroadCast);
```
