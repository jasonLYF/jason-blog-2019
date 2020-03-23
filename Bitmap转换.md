
#### Bitmap转换

###### Bitmap 转 drawable
```
Drawable newBitmapDrawable = new BitmapDrawable(bitmap);
还可以从BitmapDrawable中获取Bitmap对象
Bitmap bitmap = new BitmapDrawable.getBitmap();
```
###### drawable 转 Bitmap
- BitmapFactory 中的 decodeResource 方法
```
Resources res = getResources();
Bitmap bmp = BitmapFactory.decodeResource(res, R.drawable.ic_drawable);
```
- 将 Drable 对象先转化成 BitmapDrawable ，然后调用 getBitmap 方法 获取
```
Resource res      = gerResource();
Drawable drawable = res.getDrawable(R.drawable.ic_drawable);//获取drawable
BitmapDrawable bd = (BitmapDrawable) drawable;
Bitmap bm         = bd.getBitmap();
```
- 根据已有的Drawable创建一个新的Bitmap
```
public static Bitmap drawableToBitmap(Drawable drawable) {

    int w = drawable.getIntrinsicWidth();
    int h = drawable.getIntrinsicHeight();
    System.out.println("Drawable转Bitmap");
    Bitmap.Config config =
            drawable.getOpacity() != PixelFormat.OPAQUE ? Bitmap.Config.ARGB_8888
                    : Bitmap.Config.RGB_565;
                    
    Bitmap bitmap = Bitmap.createBitmap(w, h, config);
    
    //注意，下面三行代码要用到，否则在View或者SurfaceView里的canvas.drawBitmap会看不到图
    Canvas canvas = new Canvas(bitmap);
    drawable.setBounds(0, 0, w, h);
    drawable.draw(canvas);

    return bitmap;
}
```

###### 图片旋转指定角度

```
private Bitmap rotateImage(Bitmap image, final int degree) {
    int width = image.getWidth();
    int height = image.getHeight();
    if (width > height) {
        Matrix matrix = new Matrix();
        matrix.postRotate(degree);
        if (image != null && !image.isRecycled()) {
            Bitmap resizedBitmap = Bitmap.createBitmap(image, 0, 0, width, height, matrix, true);
            return resizedBitmap;
        } else {
            return null;
        }
    } else {
        return image;
    }
}
```

###### 图片圆角
```
public Bitmap toRoundCorner(Bitmap bitmap, int pixels) {
        Bitmap roundCornerBitmap = Bitmap.createBitmap(bitmap.getWidth(), bitmap.getHeight(), Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(roundCornerBitmap);
        int color = 0xff424242;// int color = 0xff424242;
        Paint paint = new Paint();
        paint.setColor(color);
        // 防止锯齿
        paint.setAntiAlias(true);
        Rect rect = new Rect(0, 0, bitmap.getWidth(), bitmap.getHeight());
        RectF rectF = new RectF(rect);
        float roundPx = pixels;
        // 相当于清屏
        canvas.drawARGB(0, 0, 0, 0);
        // 先画了一个带圆角的矩形
        canvas.drawRoundRect(rectF, roundPx, roundPx, paint);
        paint.setXfermode(new PorterDuffXfermode(PorterDuff.Mode.SRC_IN));
        // 再把原来的bitmap画到现在的bitmap！！！注意这个理解
        canvas.drawBitmap(bitmap, rect, rect, paint);
        return roundCornerBitmap;
    }
```
###### 图片的放大和缩小

```
public Bitmap scaleMatrixImage(Bitmap oldbitmap, float scaleWidth, float scaleHeight) {
    Matrix matrix = new Matrix();
    matrix.postScale(scaleWidth,scaleHeight);// 放大缩小比例
    Bitmap ScaleBitmap = Bitmap.createBitmap(oldbitmap, 0, 0, oldbitmap.getWidth(), oldbitmap.getHeight(), matrix, true);
    return ScaleBitmap;
}
```

###### 图片裁剪
```
public Bitmap cutImage(Bitmap bitmap, int reqWidth, int reqHeight) {
    Bitmap newBitmap = null;
    if (bitmap.getWidth() > reqWidth && bitmap.getHeight() > reqHeight) {
        bitmap = Bitmap.createBitmap(bitmap, 0, 0, reqWidth, reqHeight);
    } else {
        bitmap = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight());
    }
    return bitmap;
}
```

###### 图片合成
```
private Bitmap createStarBitmap(float grade, int maxGrade) {
        Bitmap empty_star = BitmapFactory.decodeResource(getResources(), R.drawable.empty_star); // 空星
        Bitmap normal_star = BitmapFactory.decodeResource(getResources(), R.drawable.normal_star); // 实星
        Bitmap half_star = BitmapFactory.decodeResource(getResources(), R.drawable.half_star);
        ; // 半星
        int star_width = empty_star.getWidth();
        int star_height = empty_star.getHeight();
        Bitmap newb = Bitmap.createBitmap(star_width * 5, star_height, Bitmap.Config.ARGB_8888);// 创建一个底层画布
        Canvas cv = new Canvas(newb);
        for (int i = 0; i < maxGrade; i++) {
            if (i < grade && i + 1 > grade) // 画半星
            {
                cv.drawBitmap(half_star, star_width * i, 0, null);// 画图片的位置
            } else if (i < grade) // 画实心
            {
                cv.drawBitmap(normal_star, star_width * i, 0, null);// 画图片的位置
            } else
            // 画空心
            {
                cv.drawBitmap(empty_star, star_width * i, 0, null);// 画图片的位置
            }
        }
        // save all clip
        cv.save(Canvas.ALL_SAVE_FLAG);// 保存
        // store
        cv.restore();// 存储
        return newb;
    }

activity中调用
Bitmap bm = createStarBitmap(3.5f, 5);
imageview.setImageBitmap(bm);
```
上述代码展示的是通过右图的三张图片动态合成评分组件：
![QQ截图20160628161121.png](http://upload-images.jianshu.io/upload_images/1479978-5bdfc27368334a23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 图片保存到sd
```
public void savePic(Bitmap bitmap,String path) {
        File file = new File(path);
        FileOutputStream fileOutputStream = null;
        try {
            file.createNewFile();
            fileOutputStream = new FileOutputStream(file);
            bitmap.compress(Bitmap.CompressFormat.PNG, 100, fileOutputStream);
            fileOutputStream.flush();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fileOutputStream != null) {
                    fileOutputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```


