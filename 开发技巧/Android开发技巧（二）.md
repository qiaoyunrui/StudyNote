# Android 开发技巧

## Shared Element Transition（页面共享元素动画）

### 效果图

![效果图](http://ww3.sinaimg.cn/mw690/005TG3l2jw1fa8xberoczg30u01hcb29.gif)

### 实现

效果上虽然看上去两个界面共享了一个控件，其实并不是一个控件，仍然是两个控件，效果图中的两个界面的两个控件分别是：`CradView`和`Toolbar`。

接下来就教大家如何实现：

首先是xml中的实现，两个要共享（仅仅是视觉上的）的控件必须要设置这样一个属性：`android:transitionName`，内容是一个字符字符串，但是两个控件的`transitionName`内容必须一样。

如下：

*第一个界面中的CardView*
```html
<android.support.v7.widget.CardView
    android:id="@+id/cv_main_frag_search"
    android:layout_width="match_parent"
    android:layout_height="?actionBarSize"
    android:layout_margin="@dimen/activity_horizontal_margin"
    android:clickable="true"
    android:foreground="?selectableItemBackground"
    android:transitionName="@string/tn_01">

    ...

</android.support.v7.widget.CardView>
```

*第二个界面的Toolbar*
```html
<android.support.v7.widget.Toolbar
    android:id="@+id/tb_search"
    android:layout_width="match_parent"
    android:layout_height="?actionBarSize"
    android:layout_margin="@dimen/act_search_tb_margin"
    android:background="@android:color/white"
    android:transitionName="@string/tn_01">

    ...

</android.support.v7.widget.Toolbar>  
```

然后是Java代码：

```Java
Intent mSearchIntent = new Intent(getContext(), SearchActivity.class);
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
    ActivityOptionsCompat options =
            ActivityOptionsCompat.makeSceneTransitionAnimation(getActivity(),
                    mCVSearch, getString(R.string.tn_01));
    ActivityCompat.startActivity(getActivity(), mSearchIntent, options.toBundle());
} else {
    startActivity(mSearchIntent);
}
```

就可以了。
