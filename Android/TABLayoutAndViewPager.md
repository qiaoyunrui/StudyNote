---
title: TabLayout与ViewPager的联合使用
date: 2016-04-09 14:21:39
tags: Android
---
## TabLayout
TabLayout是Google官方的design-support jar包中的一个组件。
如图所示：
![TabLayout](http://3.bp.blogspot.com/-hEsy8I3DVrA/ViFFalr14kI/AAAAAAAAL74/nfZd4behDE0/s1600/material-tabs.png)

但是TabLayout有一个致命的缺点，那就是没办法进行滑动切换，只能通过点击切换。为了弥补这个缺点，google提供了一个方法可以将TabLayout与ViewPager结合在一起，实现滑动切换的效果。

### TabLayout的一些属性
* tabMaxWidth: tab tiem的最大宽度，当app:tabMode="fixed"时不起作用
* tabIndicatorColor: TabLayout指示器的颜色
* tabIndicatorHeight: TabLayout指示器的高度
* tabPaddingStart: 距离开始的长度
* tabPaddingEnd: 距离结束的长度
* tabBackground: 背景
* tabTextAppearance: TableLayout title字体属性
* tabSelectedTextColor: 当前选择的字体的tab的字体颜色


## 如何使用
---
### XML
``` xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".activities.MainActivity">

    <android.support.design.widget.TabLayout
        android:id="@+id/tablayout"
        android:layout_width="match_parent"
        android:layout_height="?android:actionBarSize"
        android:background="@color/primary"
        app:tabMode="fixed"
        app:tabSelectedTextColor="@color/accent" />

    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_below="@id/tablayout" />

</RelativeLayout>
```

### 适配器

``` java
public class VPAndTLAdapter extends FragmentStatePagerAdapter {

    private List<String> tabNames;
    private List<BaseFragment> fragments;

    public VPAndTLAdapter(FragmentManager fm, List<String> tabNames, List<BaseFragment> fragments) {
        super(fm);
        this.tabNames = tabNames;
        this.fragments = fragments;
    }
    @Override
    public Fragment getItem(int position) {
        return fragments.get(position);
    }
    @Override
    public int getCount() {
        return fragments.size();
    }
    /**
    *这个函数就是给TabLayout的Tab设定Title
    */
    @Override
    public CharSequence getPageTitle(int position) {
        return tabNames.get(position);
    }
}
```

### Fragment
TabLayout的每一个Tab就对应着与一个Fragment

```java
public class GraphFragment extends Fragment {

    private static final String TAG = "GraphFragment";
    private View rootView;

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        rootView = inflater.inflate(R.layout.fragment_graph, container, false);
        return rootView;
    }
    @Override
    public void onStart() {
        super.onStart();
    }
}
```

### MainActivity

```java
private static final String TAB_NAME_1 = "流量图像";
private static final String TAB_NAME_2 = "监控视频";
private static final String TAB_NAME_3 = "个人中心";
private List<String> tabNames;
private List<BaseFragment> fragments;

private void initTablayoutAndViewPager() {
        tabNames = new ArrayList<>();
        fragments = new ArrayList<>();
        tabNames.add(TAB_NAME_1);
        tabNames.add(TAB_NAME_2);
        tabNames.add(TAB_NAME_3);

        mVideoFragment = new VideoFragment();
        mPersonFragment = new PersonFragment();
        mGraphFragment = new GraphFragment();
        fragments.add(mGraphFragment);
        fragments.add(mVideoFragment);
        fragments.add(mPersonFragment);

        VPAndTLAdapter mVpAndTLAdapter = new VPAndTLAdapter(getSupportFragmentManager(), tabNames, fragments);
        mViewPager.setAdapter(mVpAndTLAdapter);
        mTabLayout.setupWithViewPager(mViewPager);
    }
```

这样就可以实现滑动切换tab的效果啦。

### 缺陷&尝试

虽然基本上实现了滑动Tab的效果，但是有一个缺点，那就是无法为Tab设置图标。

之前我尝试过这样添加图标，但是失败了：
``` java
for (String tabName : tabNames) {
    mTabLayout.addTab(mTabLayout.newTab().setIcon(getResources().getDrawable(R.mipmap.ic_launcher)));
}
```
这种方法失败的原因是：之前设置的tab被适配器类中getPageTitle()覆盖了，所以无法显示图片。

#### SpannableString介绍

想要在Tab上显示图片就要用到这个类。
首先，SpannableString这个类实现了CharSequence这个接口，所以可以在Adapter中的getPageTitle()中返回。

那么，SpannableString这个类的作用到底有哪些呢？最简单的来说，它可以实现在字符串中插入一些别的东西，例如表情，颜色，甚至是HTML中的某些标签。例如：QQ的对话View（应该是这样实现的）

##### 构造方法
SpannableString的构造方法需要一个参数：CharSequence（没错就是这个超级字符串的基本显示内容）
```java
public SpannableString(CharSequence source) {
    super(source, 0, source.length());
}
```

##### setSpan()方法
setSpan()就是这个类的核心内容了，源码如下：
```java
public void setSpan(Object what, int start, int end, int flags) {
    super.setSpan(what, start, end, flags);
}
```
参数：
* 第一个参数为要向这个超级字符串中添加的内容。例如：前景色、背景色、图片、链接、下划线等等，稍后为大家展示
* 第二个参数为开始的位置（0为开始）
* 第三个参数为结束的位置
* 最后一个参数用来标识在span范围内的文本前后输入新的字符时是否也应用这个效果，分别有：
 * Spanned.SPAN_EXCLUSIVE_EXCLUSIVE(前后都不包括)
 * Spanned.SPAN_INCLUSIVE_EXCLUSIVE(前面包括，后面不包括)
 * Spanned.SPAN_EXCLUSIVE_INCLUSIVE(前面不包括，后面包括)
 * Spanned.SPAN_INCLUSIVE_INCLUSIVE(前后都包括)

##### 例子

添加前景色
```java
spannableString.setSpan(new ForegroundColorSpan(Color.BLUE), 1, 2, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

添加背景色
```java
spannableString.setSpan(new BackgroundColorSpan(Color.RED), 1, 2, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

添加下划线
```java
spannableString.setSpan(new UnderlineSpan(), 0, 5, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```

添加图片
```java
spannableString.setSpan(new ImageSpan(mContext, R.id.ic_launcher, 0, 1, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
```
....

### 开始改造

首先要修改Adapter的构造方法
#### Adapter
```java
private List<String> tabNames;
private List<BaseFragment> fragments;
private List<Integer> tabIcs;
private SpannableString spannableString;
private Context mContext;

public VPAndTLAdapter(FragmentManager fm, Context mContext, List<String> tabNames, List<BaseFragment> fragments, List<Integer> tabIcs) {
    super(fm);
    this.mContext = mContext; //获取图片资源的时候需要Context参数
    this.fragments = fragments;
    this.tabNames = tabNames; //每个Tab上的文字、可以为null
    this.tabIcs = tabIcs; //每个Tab上的图标，可以为null
}
```

接着就是修改getPageTitle()这个方法：
```java
@Override
public CharSequence getPageTitle(int position) {
    if (tabNames == null) {     //不设置文字
        spannableString = new SpannableString(" ");  //这里要给图片留一个字符的空间。
    } else {
        spannableString = new SpannableString(" " + tabNames.get(position));
    }
    if (tabIcs != null) {    //设置图标
        spannableString.setSpan(new ImageSpan(mContext, tabIcs.get(position)), 0, 1, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
    }
    return spannableString;
}
```

#### 设置TabLayout的属性
不得不说，这个问题耗了我好长时间:(

首先在styles.xml文件内定义这样一个样式：

```xml
<style name="MyCustomTextAppearance" parent="TextAppearance.Design.Tab">
      <item name="textAllCaps">false</item>
      <item name="android:textAllCaps">false</item>
      <!-- 注意这两个属性一定要写，缺一不可，否则显示不出图片！ -->
</style>
```

接着设置TabLayout的属性：
```xml
app:tabTextAppearance="@style/MyCustomTextAppearance"
```


### OK！到这里就结束啦！加油，各位!
