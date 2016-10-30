# Palette - 调色板

## 介绍

* 从一张Bitmap图片中提取对应的颜色

## 依赖

```java
dependencies {
  compile 'com.android.support:palette-v7:25.0.0' //最新
}
```

## 使用方式

### 获取对象

获取Palette的对象，有四种方式：

```java
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.ic_launcher);

Palette p1 = Palette.generate(bitmap);  //1

Palette p2 = Palette.generate(bitmap, 24);  //2

Palette.generateAsync(bitmap, new Palette.PaletteAsyncListener() {
    @Override
    public void onGenerated(Palette palette) {

    }
}); //3

Palette.generateAsync(bitmap, 24, new Palette.PaletteAsyncListener() {
    @Override
    public void onGenerated(Palette palette) {

    }
}); //4
```

PS:

1. 前两种方法为同步获取，后两种方式是异步获取。异步获取到的Palette在onGenerated方法的参数中，因此颜色设定的逻辑也在这个方法里。

2. 在获取Palette对象的时候，可以指定它的size，一般size越大获取需要的时间也就越长。

**注意：上面的四种方式都是不推荐的。下面的才是推荐使用的创建对象的方式！！**

```java
Palette.from(bitmap).generate(new Palette.PaletteAsyncListener() {
    @Override
    public void onGenerated(Palette palette) {

    }
});
```

### 获取样本swatch

可以通过Palette对象获取样本，共有6种样本。

```java
Palette.Swatch s1 = p.getVibrantSwatch();       //获取到充满活力的这种色调
Palette.Swatch s2 = p.getDarkVibrantSwatch();    //获取充满活力的黑
Palette.Swatch s3 = p.getLightVibrantSwatch();   //获取充满活力的亮
Palette.Swatch s4 = p.getMutedSwatch();           //获取柔和的色调
Palette.Swatch s5 = p.getDarkMutedSwatch();      //获取柔和的黑
Palette.Swatch s6 = p.getLightMutedSwatch();    //获取柔和的亮
```

### 获取样本中的颜色


* getPopulation() - the amount of pixels which this swatch represents.

* getRgb() - the RGB value of this color.

* getHsl() - the [HSL](http://baike.baidu.com/view/1508629.htm) value of this color.

* getBodyTextColor() - the RGB value of a text color which can be displayed on top of this color.

* getTitleTextColor() - the RGB value of a text color which can be displayed on top of this color.
