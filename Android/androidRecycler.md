---
title: RecyclerView的基本使用
date: 2016-07-22 10:32:12
tags: Android
---

# RecyclerView 基本使用

# 介绍

RecyclerView与ListView原理是类似的：都是仅仅维护少量的View并且可以展示大量的数据集。

RecyclerView用如下两种方式简化了数据的展示和处理：

* 使用LayoutManager来确定每一个item的排列方式

* 为增加和删除项目提供默认的动画效果

当然，LayoutManager和动画效果都是可以自定义的。

RecyclerView的组件：

* Adapter：需要继承RecyclerView.Adapter，作用是将数据与每一个item的界面进行绑定。

* LayoutManager：用来确定每个item如何进行排列摆放，何时展示和隐藏。回收或重用一个View的时候，LayoutManager会向适配器请求新的数据来替换旧的数据，这种机制避免了创建过多对的View和频繁的调用FindViewById方法（和ListView原理类似）。

默认有三种LayoutManager：
* LinearLayoutManager
* GridLayoutManager
* StaggeredGridLayoutManager

# 使用

## 添加依赖

```Groovy
dependencies {
  compile "com.android.support:recyclerview-v7:24.1.0"
}
```

## 布局文件

activity.xml
```html
<android.support.v7.widget.RecyclerView
        android:id="@+id/rv_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
</android.support.v7.widget.RecyclerView>
```

item.xml
```html
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:background="@color/shadow_color"
    android:orientation="horizontal"
    android:paddingBottom="@dimen/list_item_padding"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/list_item_padding">

    <TextView
        android:id="@+id/tv_title"
        android:layout_width="@dimen/item_title_width"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="@dimen/activity_horizontal_margin"
        android:text="姓名"
        android:textAppearance="@style/TextAppearance.AppCompat.Title" />

    <TextView
        android:id="@+id/tv_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="@dimen/activity_horizontal_margin"
        android:text="李狗蛋"
        android:textAppearance="@style/TextAppearance.AppCompat.Title" />

</LinearLayout>
```

## 代码

首先通过FindViewById获取这个RecyclerView：

```java
mRecyclerView = (RecyclerView)findViewById(R.id.rv_list);
```

然后创建LayoutManager，在这里创建一个LinearLayoutManager，并为RecyclerView设置LayoutManager：

```java
LinearLayoutManager mLayoutManager = new LinearLayoutManager(getContext());
mLayoutManager.setOrientation(LinearLayoutManager.VERTICAL);    //设置排列方式为垂直
mRecyclerView.setLayoutManager(mLayoutManager);
//如果可以确定每个item的高度是固定的，设置这个选项可以提高性能
mRecyclerView.setHasFixedSize(true);
```

设置Adapter
```java
//创建数据
List<SalaryDetail> list = new ArrayList<>();
for (int i = 0; i < 10; i++) {
  list.add(new SalaryDetail("姓名" + i, "张全蛋" + i));
}
QueryAdapter adapter = new QueryAdapter(list);
//设置Adapter
mRecyclerView.setAdapter(adapter);
```

### 创建Adapter继承于RecyclerView.Adapter

```java
package com.juhezi.salaryquery.query;

import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import com.juhezi.salaryquery.R;
import com.juhezi.salaryquery.data.SalaryDetail;

import java.util.ArrayList;
import java.util.List;

/**
 * Created by qiaoyunrui on 16-7-22.
 */
public class QueryAdapter extends RecyclerView.Adapter<QueryAdapter.ViewHolder> {

    private List<SalaryDetail> list = new ArrayList<>();

    public QueryAdapter(List<SalaryDetail> list) {
        this.list = list;
    }

    //创建新View，被LayoutManager所调用
    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.query_list_item, parent, false);
        ViewHolder viewHolder = new ViewHolder(view);
        return viewHolder;
    }

    //将数据与界面进行绑定
    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        if (null != list) {
            holder.mTvTitle.setText(list.get(position).getTitle());
            holder.mTvContent.setText(list.get(position).getContent());
        }
    }

    //获取数据的数量
    @Override
    public int getItemCount() {
        return list.size();
    }

    public List<SalaryDetail> getList() {
        return list;
    }

    public void setList(List<SalaryDetail> list) {
        this.list = list;
    }

    public static class ViewHolder extends RecyclerView.ViewHolder {

        public TextView mTvTitle;
        public TextView mTvContent;

        public ViewHolder(View itemView) {
            super(itemView);
            mTvTitle = (TextView) itemView.findViewById(R.id.tv_title);
            mTvContent = (TextView) itemView.findViewById(R.id.tv_content);
        }
    }
}

```

# 效果图

![pic](http://ww3.sinaimg.cn/mw690/005TG3l2jw1f62h8tjcidj30u01hcn11.jpg)
