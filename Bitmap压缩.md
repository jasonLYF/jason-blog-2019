
#### 压缩格式
##### Bitmap.CompressFormat.JPEG
JPEG：一种有损压缩（JPEG2000既可以有损也可以无损），".jpg"或者".jpeg";  
优点：采用了直接色，有丰富的色彩，适合存储照片和生动图像效果；  
缺点：有损，不适合用来存储logo、线框类图
##### Bitmap.CompressFormat.PNG
一种无损压缩，".png";  
优点：支持透明、无损，主要用于小图标，透明背景等；  
缺点：若色彩复杂，则图片生成后文件很大；
##### Bitmap.CompressFormat.WEBP
以WebP算法进行压缩；Google开发的新的图片格式，同时支持无损和有损压缩，使用直接色。  
无损压缩，相同质量的webp比PNG小大约26%；  
有损压缩，相同质量的webp比JPEG小25%-34% 支持动图，基本取代gif


#### Bitmap压缩

##### 质量压缩
质量压缩是在保持像素的前提下改变图片的位深及透明度等，来达到压缩图片的目的，经过它压缩的图片文件大小(kb)会有改变，但是导入成bitmap后占得内存是不变的，宽高也不会改变。

质量压缩不会改变图片的像素点，即我们使用完质量压缩后，在转换Bitmap时占用内存依旧不会减小。
但是可以减少我们存储在本地文件的大小，即放到 disk上的大小

因为要保持像素不变，所以它就无法无限压缩，到达一个值之后就不会继续变小了，所以这个方法并不适用与缩略图，也不适用于减少内存，只适用于减少文件大小


```
private void compressImage(Bitmap image, int reqSize) {
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        image.compress(Bitmap.CompressFormat.JPEG, 100, baos);// 质量压缩方法，这里100表示不压缩，
        int options = 100;
        while (baos.toByteArray().length / 1024 > reqSize) { // 循环判断压缩后的图片是否大于reqSize，大于则继续压缩
            baos.reset();//清空baos
            image.compress(Bitmap.CompressFormat.JPEG, options, baos);// 这里压缩options%，把压缩后的数据放到baos中
            options -= 10;
        }
        // 把压缩后的baos放到ByteArrayInputStream中
        ByteArrayInputStream isBm = new ByteArrayInputStream(baos.toByteArray());
        //decode图片
        Bitmap bitmap = BitmapFactory.decodeStream(isBm, null, null);
    }
```

```
public static Bitmap compressbyQuality(Bitmap image, Bitmap.CompressFormat compressFormat) {

    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    //质量压缩方法，这里100表示不压缩，把压缩后的数据存放到baos中
    image.compress(compressFormat, 100, baos);
    int quality = 100;

    //循环判断如果压缩后图片是否大于100kb,大于继续压缩
    while ( baos.toByteArray().length / 1024 > 100) { 
        baos.reset();//重置baos即清空baos
        if(quality > 10){
            quality -= 20;//每次都减少20
        }else {
            break;
        }
            
        //这里压缩options%，把压缩后的数据存放到baos中
        image.compress(Bitmap.CompressFormat.JPEG,quality,baos);
    }
        
    //把压缩后的数据baos存放到ByteArrayInputStream中
    ByteArrayInputStream isBm = new ByteArrayInputStream(baos.toByteArray());

    BitmapFactory.Options options = new BitmapFactory.Options();
    options.inPreferredConfig = Bitmap.Config.RGB_565;
        
    //把ByteArrayInputStream数据生成图片
    Bitmap bmp = BitmapFactory.decodeStream(isBm, null, options);

    return bmp;
}
```


##### 采样压缩（inSampleSize）（尺寸压缩）
这个方法主要用在图片资源本身较大，或者适当地采样并不会影响视觉效果的条件下，这时候我们输出的目标可能相对的较小，对图片的大小和分辨率都减小。

```
BitmapFactory.Options options = new Options();
options.inSampleSize = 2;
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), resId, options);
```

完整代码

```
public void decodeResource(View view) {
    Bitmap bm = decodeBitmapFromResource();
    imageview.setImageBitmap(bm);
}

private Bitmap decodeBitmapFromResource(){
    BitmapFactory.Options options = new BitmapFactory.Options();
    options.inJustDecodeBounds = true;
    BitmapFactory.decodeResource(getResources(), R.drawable.bbbb, options);
    options.inSampleSize = calculateSampleSize(options,300,300);
    options.inJustDecodeBounds =false;
    return  BitmapFactory.decodeResource(getResources(),R.drawable.bbbb,options);
}

// 计算合适的采样率(当然这里还可以自己定义计算规则)，reqWidth为期望的图片大小，单位是px
private int calculateSampleSize(BitmapFactory.Options options,int reqWidth,int reqHeight){
    Log.i("========","calculateSampleSize reqWidth:"+reqWidth+",reqHeight:"+reqHeight);
    int width = options.outWidth;
    int height =options.outHeight;
    Log.i("========","calculateSampleSize width:"+width+",height:"+height);
    int inSampleSize = 1;
    int halfWidth = width/2;
    int halfHeight = height/2;
    while((halfWidth/inSampleSize)>=reqWidth&& (halfHeight/inSampleSize)>=reqHeight){
        inSampleSize*=2;
        Log.i("========","calculateSampleSize inSampleSize:"+inSampleSize);
    }
    return inSampleSize;
}
```



##### 使用矩阵

##### 合理选择Bitmap的像素格式