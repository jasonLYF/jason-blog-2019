##### 简介
Bitmap 代表一个位图，BitmapDrawable 里封装的图片就是一个 Bitmap 对象

```
BitmapDrawable bitmapDrawable = new BitmapDrawable(bitmap);
Bitmap bitmap = bitmapDrawable.getBitmap();
```


#### 创建

###### 方式一、用Bitmap的静态方法createBitmap()创建
Bitmap 构造方法默认权限，而且这个类也是 final 的，所以我们无法 new 一个 Bitmap 对象。  
可以根据静态方法 createBitmap 来创建 Bitmap 类。  
这些重载的方法有13个,大致分为三类：
1. 根据已有的Bitmap来创建新Bitmap
2. 通过像素点数组创建空的Bitmap
3. 创建缩放的Bitmap

###### 方式二、BitmapFactory的decode系列静态方法创建  

1. decodeFile(String pathName, Options opts) 从文件系统中解析
    a. 通过Intent打开本地图片或照片  
    b. 在onActivityResult中获取图片uri  
    c. 根据uri获取图片的路径  
    d. 根据路径解析bitmap:Bitmap bm = BitmapFactory.decodeFile(sd_path)
2. decodeResource 以R.drawable.xxx的形式从本地资源中加载
```
Bitmap bm = BitmapFactory.decodeResource(getResources(), R.drawable.aaa);
```
> 加载的图片可能会经过缩放，该缩放目前是放在 java 层做的，效率比较低，而且需要消耗 java 层的内存。因此，如果大量使用该接口加载图片，容易导致OOM错误。
3. decodeStream 从输入流加载
    a. 开启异步线程去获取网络图片  
    b. 网络返回InputStream  
    c. 解析：`Bitmap bm = BitmapFactory.decodeStream(stream)`,这是一个耗时操作，要在子线程中执行
4. decodeByteArray(byte[] data, int offset, int length, Options opts)  
    a. 把InputStream转换成byte[]  
    b. 解析：`Bitmap bm = BitmapFactory.decodeByteArray(myByte,0,myByte.length)`;

> 不会对所加载的图片进行缩放，相比之下占用内存少，效率更高

5. decodeFileDescriptor(FileDescriptor fd, Rect outPadding, Options opts)：  
用于从 FileDescriptor 对应的文件中解析、创建 Bitmap 对象

> decodeFile和decodeResource间接调用decodeStream方法
