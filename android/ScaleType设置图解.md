## for Android: ImageView.ScaleType | ImageView.ScaleType设置图解 
>
- 设置方式
- ScaleType各类型图解

#### 设置方式
1. 在layout xml中定义
```xml
android:scaleType="center" 
```  
2. 或在代码中调用 
```java 
imageView.setScaleType(ImageView.ScaleType.CENTER);  
```

#### ScaleType各类型图解
测试图片：
![image](../images-folder/0_1319976827wW01.png)
(Dimensions: 128 * 128)  
![image](../images-folder/0_1319976939pkgr.gif)
(Dimensions: 640 * 428)  

1. imageView.setScaleType(ImageView.ScaleType.CENTER);  
   按图片的原来size居中显示，当图片长/宽超过View的长/宽，则截取图片的居中部分显示
![image](../images-folder/center1.jpg) ![image](../images-folder/center2.gif)
