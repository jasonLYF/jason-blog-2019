##### View的坐标参数
1. top 左上角纵坐标	left左上角横坐标	bottom右下角纵坐标	right右下角横坐标
2. x、y是View左上角的坐标
3. translationX、translationY是View左上角相对于父容器的偏移量，默认值都为0
4. getX/getY 返回的是相对于当前view左上角的x和y
5. getRawX/getRawY 返回的是相对于手机屏幕左上角的x和y坐标

**关系**  
x=left+translationX； 
```
y=top+translationY；
width = right-left;
height = bottom-top;
Left = getLeft()
```

View平移时，top、left等是原始左上角位置信息，其值不会发生变化，此时发生改变的是x、y、translationX、translationY

##### View中的几个重要方法
1. `public final void measure(int widthMeasureSpec, int heightMeasureSpec)`
- measure 过程决定了View的宽高，Measure完成后可以通过getMeasureWidth和getMeasureHeight方法获取到view的测量后的宽高，在几乎所有的情况下都会等于最终view的宽高
- measure()方法接收两个参数，widthMeasureSpec和heightMeasureSpec，这两个值分别用于确定视图的宽度和高度的规格和大小。
2. `protected void onLayout(boolean changed, int left, int top, int right, int bottom)`
- layout 过程决定了View的四个顶点的坐标和实际的View的宽高，完成以后可以通过getTop,getBottom,getLeft,getRight来获取View的四个顶点位置，并通过getWidth,getHeight获取View的最终宽高
3. `protected void onDraw(Canvas canvas)`
draw过程则决定了View的显示，完成draw后view会显示在屏幕上
- 绘制背景(background.draw(Canvas))
- 绘制自己  
protected void onDraw(Canvas canvas)
onDraw绘制自己，新建一个paint 在canvas上绘制自己的图形
- 绘制children (dispatchDraw)
dispatchDraw会遍历调用所有子元素的draw方法
- 绘制装饰（onDrawScrollBars）
4. `public boolean isEnabled()`  
当前视图是否可用。  
可以调用setEnable()方法来改变视图的可用状态，传入true表示可用，传入false表示不可用。
它们之间最大的区别在于，不可用的视图是无法响应onTouch事件的。
5. `public boolean isFocused()`  
当前视图是否获得焦点  
通常情况下有两种方法可以让视图获得焦点，即通过键盘的上下左右键切换视图，以及调用requestFocus()方法。而现在的Android手机几乎都没有键盘了，因此基本上只可以使用requestFocus()这个办法来让视图获得焦点了。  
而requestFocus()方法也不能保证一定可以让视图获得焦点，它会有一个布尔值的返回值，如果返回true说明获得焦点成功，返回false说明获得焦点失败。一般只有视图在focusable和focusable in touch mode同时成立的情况下才能成功获取焦点，比如说EditText。
6. `public void offsetTopAndBottom(int offset)及 public void offsetLeftAndRight(int offset)`
offsetTopAndBottom直接改变的是top, bottom, 相当于在parent中上下平移View的位置；
offsetLeftAndRight直接改变的是left, right, 相当于在parent中左右平移View的位置；
View的边界直接发生了变化，又因为View和他的子View的相对位置没变，所以他的子View的边界也跟着变化了。

#### 获取View的位置坐标失败问题怎么处理
获取到位置坐标为0，可能有如下三种原因：
- view的宽高本身就是0
- View的visibility属性为gone；
- 视图还未绘制完成，当然未绘制完成也表现在不同的方面，比如，activity所代表的界面还没显示出来没有添加到WindowPhone的DecorView上；要获取的view没有被添加到DecorView上

##### 主要针对视图还未绘制完成，怎么获取view位置坐标信息
1. 在View的事件回调里获取；这时候该view已经被显示即被添加到DecorView上 如点击事件 触摸事件 焦点事件等
```
view1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
              // doing
            }
        });
```
2. 在activity被显示出来时即添加到了DecorView上时获取宽和高如 `onWindowFocusChanged()` 回调方法
```
@Override
public void onWindowFocusChanged(boolean hasFocus) {
    super.onWindowFocusChanged(hasFocus);
    // doing
}

boolean measure;
@Override
public void onWindowFocusChanged(boolean hasFocus) {
    super.onWindowFocusChanged(hasFocus);
    if (hasFocus && !measure) {
        measure = true;
        int width = new TextView(this).getMeasuredWidth();
        int height = new TextView(this).getMeasuredHeight();
    }
}
```
3. 在onResume方法最后开线程300毫秒左右后获取宽和高 因为onResume执行完后300毫秒后 界面就显示出来了
```
@Override
protected void onResume() {
    super.onResume();
    view1.postDelayed(new Runnable() {
        @Override
        public void run() {
            // doing
        }
    }, 300);
}
```
4. 在onCreate()或onResume()等方法中需要获取宽高时使用`getViewTreeObserver().addOnGlobalLayoutListener()`
来为view添加回调在回调里获得宽度或者高度获取完后让view删除该回调
```
view1.getViewTreeObserver().addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
        @Override
        public void onGlobalLayout() {
            // doing
        }
    });
```
5. view.measure(int widthMeasureSpec,int heightMeasureSpec)
手动对view进行measure来得到view的宽高

