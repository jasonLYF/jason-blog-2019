#### BitmapFactory.Options的属性
##### 1、inJustDecodeBounds  
如果这个值为 true ，那么在解码的时候将不会返回 Bitmap ，只会返回这个 Bitmap 的尺寸。  
这个属性的目的是，如果你只想知道一个 Bitmap 的尺寸，但又不想将其加载到内存中时，是一个非常好用的属性。

##### 2、outWidth和outHeight
表示这个 Bitmap 的宽和高，一般和 inJustDecodeBounds 一起使用来获得 Bitmap的宽高，但是不加载到内存。

##### 3、inSampleSize
压缩图片时采样率的值，如果这个值大于1，那么就会按照比例（1 / inSampleSize）来缩小 Bitmap 的宽和高。  
- inSampleSize的值必须大于1时才会有效果，且采样率同时作用于宽和高；inSampleSize比1小的话会被当做1，任何inSampleSize的值会被取接近2的幂值。
- 当inSampleSize=1时，采样后的图片为图片的原始大小
- 如果这个值为 2，那么 Bitmap 的宽为原来的1/2，高为原来的1/2，那么这个 Bitmap 是所占内存像素值会缩小为原来的 1/4。
- 

##### 4、inDensity
表示这个 Bitmap 的像素密

##### 5、inPreferredConfig
这个值是设置色彩模式，默认值是 ARGB_8888；这个色彩模式色彩最细腻，显示质量最高。但同样的，占用的内存也最大。  

假设一张1024\*1024，模式为ARGB_8888的图片，那么它占有的内存就是：1024\*1024\*4 = 4MB  

- ALPHA_8：每个像素占用1byte内存。
- ARGB_4444:每个像素占用2byte内存
- ARGB_8888:每个像素占用4byte内存
- RGB_565:每个像素占用2byte内存

##### 6、inScaled：
设置这个Bitmap 是否可以被缩放，默认值是 true，表示可以被缩放。

##### 7、inBitmap
这个参数用来实现 Bitmap 内存的复用，但复用存在一些限制，具体体现在：  
在 Android 4.4 之前只能重用相同大小的 Bitmap 的内存，而 Android 4.4 及以后版本则只要后来的 Bitmap 比之前的小即可。  
使用 inBitmap 参数前，每创建一个 Bitmap 对象都会分配一块内存供其使用，而使用了 inBitmap 参数后，多个 Bitmap 可以复用一块内存，这样可以提高性能。
