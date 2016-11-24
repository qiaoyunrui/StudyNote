# DrawerLayout & CoordinatorLayout & Toolbar & TabLayout的经典实现

## 效果图

![gif](http://ww1.sinaimg.cn/mw690/005TG3l2jw1fa3607u2v9g30qo1bfnpe.gif)

## 话不多说，直接上代码

```html
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/dl_act_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.juhezi.emoticon.main.MainActivity"
    tools:openDrawer="start">

    <android.support.design.widget.CoordinatorLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true">

        <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="@dimen/act_main_app_bar_layout_height"
            android:fitsSystemWindows="true">

            <android.support.design.widget.CollapsingToolbarLayout
                android:id="@+id/ctl_main_act"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:fitsSystemWindows="true"
                android:theme="@style/ThemeOverlay.AppCompat.Dark"
                app:contentScrim="@color/colorPrimary"
                app:layout_scrollFlags="scroll|exitUntilCollapsed">

                <ImageView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:clickable="true"
                    android:fitsSystemWindows="true"
                    android:foreground="?selectableItemBackgroundBorderless"
                    android:scaleType="centerCrop"
                    android:src="@drawable/google_andromeda_os"
                    app:layout_collapseMode="parallax" />

                <android.support.v7.widget.Toolbar
                    android:id="@+id/tb_main"
                    android:layout_width="match_parent"
                    android:layout_height="?actionBarSize"
                    android:theme="@style/Toolbar"
                    app:layout_collapseMode="parallax">

                </android.support.v7.widget.Toolbar>

                <android.support.design.widget.TabLayout
                    android:id="@+id/tl_act_main_tabs"
                    android:layout_width="match_parent"
                    android:layout_height="?actionBarSize"
                    android:layout_gravity="bottom"
                    app:tabMode="scrollable"
                    app:tabSelectedTextColor="@color/colorAccent"
                    app:tabTextColor="@android:color/white" />

            </android.support.design.widget.CollapsingToolbarLayout>

        </android.support.design.widget.AppBarLayout>

        <android.support.v4.view.ViewPager
            android:id="@+id/vp_act_main"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_behavior="@string/appbar_scrolling_view_behavior">

        </android.support.v4.view.ViewPager>

        <android.support.design.widget.FloatingActionButton
            android:id="@+id/fab_frag_main_add"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="right|bottom"
            android:layout_margin="@dimen/activity_horizontal_margin"
            android:clickable="true"
            android:src="@drawable/ic_add"
            app:rippleColor="@color/colorPrimary" />

    </android.support.design.widget.CoordinatorLayout>

    <android.support.design.widget.NavigationView
        android:id="@+id/nav_act_main"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:menu="@menu/menu_act_main_nav">

    </android.support.design.widget.NavigationView>


</android.support.v4.widget.DrawerLayout>
```

```Java
mCollapsingToolbarLayout = (CollapsingToolbarLayout) findViewById(R.id.ctl_main_act);
mCollapsingToolbarLayout.setTitleEnabled(false);    //使标题不与TabLayout重复

mTbMain = (Toolbar) findViewById(R.id.tb_main);
setSupportActionBar(mTbMain);
mActionBar = getSupportActionBar();
mActionBar.setTitle(getString(R.string.app_name_title));
mActionBar.setHomeButtonEnabled(true);
mActionBar.setDisplayHomeAsUpEnabled(true);
mActionBar.setHomeAsUpIndicator(R.drawable.ic_menu);

mDrawerLayout = (DrawerLayout) findViewById(R.id.dl_act_main);
mNavigationView = (NavigationView) findViewById(R.id.nav_act_main);
ActionBarDrawerToggle toggle = new ActionBarDrawerToggle(
        this, mDrawerLayout, mTbMain, R.string.app_name_title, R.string.app_name_title
);
mDrawerLayout.setDrawerListener(toggle);
toggle.syncState();
```
