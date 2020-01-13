##### 自定义View的分类

1. 继承View重写onDraw

   常用于绘制不规则的图片。这种方式需要自己实现支持wrap_content和padding

2. 继承ViewGroup实现特殊的Layout

   常用语实现组合布局

3. 继承特殊的View比如TextView

   常用语扩展View已有的功能

##### 自定义View注意事项

1. wrap_content

   直接继承View或者ViewGroup的控件，如果不在onMeasure中对wrap_content做特殊处理的话，wrap_content是失效的，这是因为在view的measure中默认实现  AT_MOST和EXACTLY俩种测量模式的效果是相同的

   解决方法：位View指定一个默认的宽高，并在wrap_content时即测量模式是AT_MOST时设置此宽高即可；

   ```java
       @Override
       protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
           int mWidth=600;
           int mHeight =600;
           int widthMode = MeasureSpec.getMode(widthMeasureSpec);
           int widthSize = MeasureSpec.getSize(widthMeasureSpec);
           int heightMode = MeasureSpec.getMode(heightMeasureSpec);
           int heightSize = MeasureSpec.getSize(heightMeasureSpec);
           if(widthMode==MeasureSpec.AT_MOST && heightMode==MeasureSpec.AT_MOST){
               setMeasuredDimension(Math.min(mWidth, widthSize),Math.min(mHeight, heightSize));
           }else if(widthMode==MeasureSpec.AT_MOST){
               setMeasuredDimension(Math.min(mWidth, widthSize),heightSize);
           }else if( heightMode==MeasureSpec.AT_MOST){
               setMeasuredDimension(widthSize,Math.min(mWidth, widthSize));
           }
       }
   ```

   

2. 让View支持padding

   直接继承View的控件，如果不再draw方法中处理padding，那么这个属性是无法起作用的。

   解决方法：在onDraw绘制时把padding考虑进去即可

   ```java
       @Override
       protected void onDraw(Canvas canvas) {
           super.onDraw(canvas);
           final int paddingLeft = getPaddingLeft();
           final int paddingRight = getPaddingRight();
           final int paddingTop = getPaddingTop();
           final int paddingBottom = getPaddingBottom();
           int width = getWidth() - (paddingLeft + paddingRight);
           int height = getHeight() - (paddingTop + paddingBottom);
           int radius = Math.min(width, height) / 2;
           canvas.drawCircle(paddingLeft+width / 2, paddingTop+height / 2, radius, mPaint);
       }
   ```



[其他自定义View相关]: https://www.jianshu.com/p/7468e038825a









