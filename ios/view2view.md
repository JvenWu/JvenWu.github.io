## for IOS: View To View | 页面跳转

下面以OldViewController(oldC)的按钮btn点击后跳转到NewViewController(newC)为例说明:

1.Storyboard的segues方式  
鼠标点击按钮btn然后按住control键拖拽到newC页面，在弹出的segue页面中选择跳转模式即可  
优点:操作方便,无代码生成,在storyboard中展示逻辑清晰  
缺点:页面较多时不方便查看,团队合作时可维护性差, 多人合作时不建议使用这种方式

2.选项卡UITabBarController控制器
```objectivec
//通过调用UITabBarController的addChildViewController方法添加子控制器
UITabBarController *tabbarVC = [[UITabBarController alloc] init];  
OldViewController *oldC = [[OldViewController] init];
oldC.tabBarItem.title = @"控制器1";
oldC.tabBarItem.image = [UIImage imageNamed:@"old.png"];
NewViewController *newC = [[NewViewController] init];
newC.tabBarItem.title = @"控制器2";
newC.tabBarItem.image = [UIImage imageNamed:@"new.png"];
//添加子控制器(这些子控制器会自动添加到UITabBarController的viewControllers数组中)
[tabbarVC addChildViewController:recent];
[tabbarVC addChildViewController:friends];
```
优点:代码量较少  
缺点:tabbar的iOS原生样式不太好看,(不常用,目前不建议使用),如果要使用,建议自定义tabbar

3.导航控制器UINavigationController
```objectivec
//在oldC的btn的监听方法中调用
[self.navigationController pushViewController:newC animated:YES]; //跳转到下一页面
//在newC的方法中调用
[self.navigationController popViewControllerAnimated:YES]; //返回上一页面 
//当有多次跳转发生并希望返回根控制器时,调用:
[ self .navigationController popToRootViewControllerAnimated: YES ];  //返回根控制器,即最开始的页面
```

4.利用 Modal 形式展示控制器
```objectivec
//在oldC中调用:
[ self presentViewController:newC animated: YES completion:nil];
//[self presentModalViewController:control animated:YES];
//在newC中调用:
[ self dismissViewControllerAnimated: YES completion: nil ];
//[self dismissModalViewControllerAnimated:YES];
```

5.无动画
```objectivec
[self.view addSubview:<(UIView *)>]
[self.view removeFromSuperview]; 
```

6.直接更改 UIWindow 的 rootViewController
```objectivec
[self.view.window addSubview:otherview];
[self.view removeFromSuperview]
```

7.从xib的viewcontroll中启动storyboard或者从一个storyboard切换到另一个storyboard
```objectivec
UIStoryboard *secondStoryboard = [UIStoryboard storyboardWithName:@"SecondStoryboard" bundle:nil];    
[self presentModalViewController:[secondStoryboard instantiateInitialViewController] animated:YES];
```

8.从storyboard切换到xib
```objectivec
LoginViewController *loginViewController = [[LoginViewController alloc] 
initWithNibName:@"LoginViewController" bundle:nil];
//然后用push或者modal方法启用这个controller
```

9.其它
```objectivec
self.fristController=[[UIStoryboard storyboardWithName:@"Main_iPhone" bundle:nil] 
instantiateViewControllerWithIdentifier:@"FristController"];  
[self.navigationController pushViewController:self.fristController animated:YES];
```

总结  
Storyboard方式适合个人开发小程序时使用,有团队合作或者项目较大时不建议使用
UITabBarController因为目前系统的原生样式不太美观,不建议使用
推荐使用UINavigationController和Modal,无明显缺点,而且目前大部分程序都使用这两种方式,
只是看是否需要导航控制器而确定使用哪种方案
