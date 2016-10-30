# BottomNavigationView

## 介绍

底部导航控件

## 依赖

```java
dependencies {
    compile 'com.android.support:design:25.0.0' //最新
}
```

## 如何使用

### 1. 新建底部导航所使用的菜单文件：

```html
<menu xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:id="@+id/bottom_menu_home"
        android:icon="@drawable/ic_home"
        android:title="home" />

    <item
        android:id="@+id/bottom_menu_chat"
        android:icon="@drawable/ic_chat"
        android:title="chat" />

    <item
        android:id="@+id/bottom_menu_search"
        android:icon="@drawable/ic_chat"
        android:title="search" />

    <item
        android:id="@+id/bottom_menu_camera"
        android:icon="@drawable/ic_camera"
        android:title="camera" />

    <item
        android:id="@+id/bottom_menu_notifications"
        android:icon="@drawable/ic_notifications"
        android:title="notifications" />

</menu>
```

### 布局文件添加

```html
<android.support.design.widget.BottomNavigationView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_alignParentBottom="true"
    app:menu="@menu/bottom_menu">

</android.support.design.widget.BottomNavigationView>
```

### 添加监听事件


使用**Koltin**语言：
```JavaScript
mBnvTest1?.setOnNavigationItemSelectedListener {
    mTvTest1?.text = it.title.toString().toUpperCase()
    true
}
```

**Java** 代码在这里：

```java
mNavigationView.setOnNavigationItemSelectedListener(
        new BottomNavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                mTextView.setText(item.getTitle().toString().toUpperCase());
                return true;
            }
});
```

## 效果图如下

![pic](http://ww3.sinaimg.cn/large/005TG3l2jw1f9a8fg3a9kj30u01hcmye.jpg)

## XML属性

* app:itemBackground - 设置状态栏的背景颜色

* app:itemIconTint - 设置图标颜色

* app:itemTextColor - 设置文字颜色
