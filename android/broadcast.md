## for Android: Broadcast | 广播的使用
```java
class ProjectUpdateBroadcastReceive extends BroadcastReceiver {
		@Override
		public void onReceive(Context context, Intent intent) {
			String actionStr = intent.getAction();
			if (TaskModifyActivity.ACTION_CREATE_TASK.equals(actionStr)
					|| TaskModifyActivity.ACTION_MODIFY_TASK.equals(actionStr)
					|| AddSprintActivity.ADD_SPRINT.equals(actionStr)
					|| PROJECT_REFRESH.equals(actionStr)) {
				mPtrFrameLayout.autoRefresh(false);
			}
		}
	}
```
