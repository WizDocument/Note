# Bitmap高效加载

主要是通过设置BitmapFactory.Options来实现

1. inSampleSize，采样率，2的倍数，减少内存开支
2. inJustDecodeBounds，不加载Bitmap的情况下，获取Bitmap的宽高属性
3. inBitmap
	- 复用内存块，不需要重新给这个Bitmap申请一块新的内存
	- 只能在3.0以后使用
	- 3.0~4.4，只能重用相同大小的Bitmap内存区域
	- 4.4以后， 只要旧的Bitmap的尺寸大于等于新的Bitmap的尺寸即可

对于inBitmap，只能当旧对象被回收后，新对象才能复用
对于Bitmap对象，3.0以下及8.0以上占用native的内存，其他版本占用java内存

# inJustDecodeBounds

```
BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeFile(path, options);
```

inJustDecodeBounds设置为true，可以直接获取bitmap的宽高，但获取的bitmap对象为null

# 图片压缩

[开源库-鲁班](https://github.com/Curzibn/Luban)

问题：不支持输出文件名的设置

# Bitmap.Config

ARGB指的是一种色彩模式，里面A代表Alpha，R表示red，G表示green，B表示blue，其实所有的可见色都是右红绿蓝组成的，所以红绿蓝又称为三原色，每个原色都存储着所表示颜色的信息值

ALPHA_8

- Alpha由8位组成
- 只有alpha通道，使用场景单一

ARGB_4444

- 由4个4位组成即16位
- 已经废弃，且创建的位图质量差

ARGB_8888

- 由4个8位组成即32位
- 能够创建高质量的图片，但是内存开销比较大

RGB_565

- R为5位，G为6位，B为5位共16位
- 能够在保证图片质量的情况下减少内存开销，唯一缺点就是没有alpha通道

# Bitmap设置Background变小的问题

代码片段

```
BitmapDrawable bd=new BitmapDrawable(bitmap);
llLeft.setBackgroundDrawable(bd);
```

这样会导致实际看到的背景远小于Bitmap实际的大小

原因是没有指定系统分辨率，需要setTargetDensity

```
BitmapDrawable bd=new BitmapDrawable(bitmap[0]);

DisplayMetrics dm = new DisplayMetrics();
getWindowManager().getDefaultDisplay().getMetrics(dm);
bd.setTargetDensity(dm);

llLeft.setBackgroundDrawable(bd);
```