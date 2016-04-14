## for Android: **Draw** use **xml** | 使用xml来绘制
```xml
<!-- 列表层，可以用于叠加绘图 -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item>
        <shape>
            <solid android:color="#a0b0f0" />
            <gradient
                android:angle="270"
                android:endColor="@color/background_color"
                android:startColor="@color/black" />
        </shape>
    </item>
    
    <item android:bottom="6dp">
        <shape>
            <solid android:color="@color/white" />
        </shape>
    </item>
    
    <!-- 条目标签，可以设置边距 -->
    <item
        android:bottom="13dp"
        android:left="13dp"
        android:right="13dp"
        android:top="13dp">
        <!-- 形状 -->
        <shape android:shape="oval" >
            <!-- 颜色填充 -->
            <solid android:color="#FFACB8C3" />
            <!-- 渐变 -->
            <gradient
                android:angle="270"
                android:endColor="#FF91c0e8"
                android:startColor="#FFa7d3fa" />
            <!-- 描边 -->
            <stroke
                android:width="6dp"
                android:color="#33b5e5" />
            <!-- 圆角 -->
            <corners
                android:bottomLeftRadius="6dip"
                android:bottomRightRadius="6dip"
                android:topLeftRadius="6dip"
                android:topRightRadius="6dip" />
            <!-- 内边距 -->
            <padding
                android:left="40dp"
                android:right="40dp" />
        </shape>
    </item>
</layer-list>
```
