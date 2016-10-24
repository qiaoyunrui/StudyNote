# RecyclerView简单分析

## 工作流程图

![pic](https://www.processon.com/chart_image/580c9798e4b03c844a5cbac0.png)

## 一些结论

* RecyclerView的Adapter中被缓存的单位是ViewHolder，而ListView中缓存的则是View

* RecyclerView.Recycler类中mAttachedScrap、mChangedScrap、mCachedViews几个ViewHolder列表对象，它们就是用于缓存ViewHolder的。

* 通过LayoutState的next函数获取Item View时，实际上调用的是RecyclerView.Recycler的getViewForPosition函数，该函数首先会从这几个ViewHolder缓存中获取对应位置的Viewholder，如果没有缓存则调用RecyclerView.Adapter中的createViewHolder函数创建一个新的ViewHolder。createViewHolder函数中回调 **onCreateViewHolder()**。

* 调用了Adapter的createViewHolder后，会调用Adapter.bindViewHolder()，在bindViewHolder函数中回调了 **onBindViewHolder()**。

* RecyclerView与ListView最大的不同是RecyclerView将布局的工作交给了LayoutManager，在LayoutManager的onLayoutChildren中对ItemView进行布局、执行动画等操作，减小了耦合，使RecyclerView有更好的扩展性。
