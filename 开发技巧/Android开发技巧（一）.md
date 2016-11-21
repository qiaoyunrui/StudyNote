# Android开发技巧（一）

  最近通过研究一个[高仿知乎](https://github.com/Kelin-Hong/MVVMLight)项目来学习MVVM框架，但是这个项目除了MVVM之外还有很多我之前一直很想知道的技巧。

## 实现DrawerLayout上汉堡按钮的动画效果
<br/>
![效果图](http://ww4.sinaimg.cn/mw690/005TG3l2jw1fa03g9nozpj30u01hc0v3.jpg)

代码实现如下：
```java
ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(
                this, mDrawerLayout, mToolbar, R.string.app_name_title, R.string.app_name_title
        );
mDrawerLayout.setDrawerListener(toggle);
```

而且，在使用了`ActionBarDrawerToggle`之后，就不需要在`onOptionsItemSelected()`中监听`android.R.id.home`的点击事件了。

## 通过返回键关闭`DrawerLayout`

重写`onBackPressed()`方法，当`DrawerLayout`处于打开状态时，先关闭`DrawerLayout`。

代码实现如下：
```java
@Override
public void onBackPressed() {
    if (mDrawerLayout.isDrawerOpen(GravityCompat.START)) {
        mDrawerLayout.closeDrawer(GravityCompat.START);
    } else {
        super.onBackPressed();
    }
}
```

## 通过Gradle动态修改app_name

```java
/**
 * 获取当前时间
 * @return 当前时间
 */
def currentTime() {
    return new Date().format("yyyy_MM_dd HH:mm:ss", TimeZone.getTimeZone("GMT+8"))
}

def app_name_release = "Emoticon"
def debug_suffix = "debug"

android{
  ...

  buildTypes {
        release {
            ...
            resValue("string", "app_name", app_name_release)
        }
        debug {
            resValue("string", "app_name", "${app_name_release}_${debug_suffix}_${currentTime()}")
        }
      }

  ...
}
```
