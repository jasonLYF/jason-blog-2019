


#### 内存

###### 色彩说明
* ALPHA_8：每个像素占用1byte内存。
* ARGB_4444:每个像素占用2byte内存
* ARGB_8888:每个像素占用4byte内存
* RGB_565:每个像素占用2byte内存

Android默认的色彩模式为ARGB_8888，这个色彩模式色彩最细腻，显示质量最高。但同样的，占用的内存也最大。

###### 内存计算
假设一张1024\*1024，模式为ARGB_8888的图片，那么它占有的内存就是：1024\*1024\*4 = 4MB

###### 获取内存
1. getByteCount()  
这个方法是在API12加入的，代表存储Bitmap的色素需要的最少内存。API19开始getAllocationByteCount()方法代替了getByteCount()。
2. getAllocationByteCount()  
API19之后，Bitmap加了一个Api：getAllocationByteCount()；代表在内存中为Bitmap分配的内存大小

**getByteCount()与getAllocationByteCount()的区别**  
一般情况下两者是相等的；  
通过复用Bitmap来解码图片，如果被复用的Bitmap的内存比待分配内存的Bitmap大,那么getByteCount()表示新解码图片占用内存的大小（并非实际内存大小,实际大小是复用的那个Bitmap的大小），getAllocationByteCount()表示被复用Bitmap真实占用的内存大小（即mBuffer的长度）





