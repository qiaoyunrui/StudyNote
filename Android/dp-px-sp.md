# dp、sp、px 的转换

## PPI

PPI(Pixels per inch，每英寸上的象素数，即“像素密度”)
* xhdpi:2.0
* hdpi:1.5
* mdpi:1.0
* ldpi:0.75

下图是 PPI 对应屏幕尺寸：

![ppi](http://img1.tbcdn.cn/L1/461/1/5a7fe24f76a7f23cbdea5efa088b1188d73597f5)

以及 PPI 计算公式：

![ppi](http://img1.tbcdn.cn/L1/461/1/af31a9366de3232f78a9e48904f5b0463875ec25)

## dp

虚拟像素，在不同的像素设备上会自动适配。

px 和 dp 之间的换算关系：

`px = dp * (dpi / 160)`

## sp

用于字体大小，为了保证设备无关性。
