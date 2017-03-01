# Android杂谈（二）

## Widgets

* 系统会对Button中的所有英文字母自动进行大写转换，`android:textAllCaps="false"`可以禁用这一默认属性

* `invisiable`是表示控件不可见，但是它仍然占据着原来的位置和大小，可以理解成控件变成透明状态了。`gone`则biash控件不仅不可见，而且不再占用任何屏幕空间。

* Android 中的一些限定符
  * 大小
    * small
    * normal
    * large
    * xlarge
  * 分辨率
    * ldpi - 120dpi以下
    * mdpi - 120~160dpi
    * hdpi - 160~240dpi
    * xhdpi - 240dpi~320dpi
    * xxhdpi - 320~480dpi
  * 方向
    * land - 横屏
    * port - 竖屏
