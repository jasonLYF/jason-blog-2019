##### View的绘制

View的绘制流程是从ViewRoot的performTraversals方法开始，performTraversals会依次调用performMeasure，performLayout，performDraw，这三个方法分别完成View的measure，layout，draw三个流程；

performMeasure会调用measure方法，而measure方法又回调用onMeasure，在onMeasure中完成对View的测量；

measure决定了View的宽和高，measure完成后那，可以通过getMeasureWidth和getMeasureHeight来获取View测量后的宽高，通常情况下它都等于View最终的宽高；

layout决定了View四个顶点的坐标和View实际的宽高，layout阶段决定了view的最终大小，layout完成后可以通过getTop、getLeft等来获取View四个顶点的坐标，并可通过getWidth和getHeight来获取View最终的宽和高；

draw方法决定了VIew的显示，只有draw完成后view的内容才能呈现在屏幕上；

View的绘制分为以下几个步骤：绘制背景、绘制自己、绘制子view、绘制装饰

###### getMeasureWidth 与 getWidth 的区别？

二者的赋值时机不同，getMeasureWidth表示测量宽高，在measure时赋值，而getWidth表示最终宽高，在layout时赋值；在View的默认实现中测量宽高和最终宽高是相等的，只是在某些情况下会导致俩者的值不一样，比如：在layout过程中修改view的宽高



##### MeasureSpec

MeasureSpec很大程度上决定了一个View的尺寸规格，之所以说是很大程度上是因为这个过程还受父容器的影响，因为父容器影响view的MeasureSpec的创建过程。

MeasureSpec代表一个32位的int值，其中高2位代表SpecMode，低30位代表SpecSize；

**SpecMode有三类：**

- UNSPECIFIED 父容器不对view有任何限制，要多大给多大
- EXACTLY 父容器已经检测出view的最终大小，这个时候view的最终大小就是SpecSize指定的值，它对应LayoutParams中的match_parent
- AT_MOST 父容器指定了一个可用大小即SpecSzie，view的大小不能大于这个值，具体是什么值要看View的具体实现，它对应LayutParams中的wrap_content



父View match_parent，子View wrap_content，最终结果就是match_parent

父View match_parent，子TextView wrap_content，最终结果就是wrap_content



##### 获取View的位置坐标失败问题怎么处理

可能造成获取的值为0的情况

- view的宽高本身就是0
- View的visibility属性为gone
- 视图还未绘制完成   当然未绘制完成也表现在不同的方面，比如，activity所代表的界面还没显示出来没有添加到WindowPhone的DecorView上；要获取的view没有被添加到DecorView上。

我们主要记录第三种情况的处理

1. 在View的事件回调里获取；这时候该view已经被显示即被添加到DecorView上 如点击事件 触摸事件 焦点事件等

   ```java
    view1.setOnClickListener(new View.OnClickListener() {
               @Override
               public void onClick(View v) {
                 // doing
               }
           });
   ```

2. 在activity被显示出来时即添加到了DecorView上时获取宽和高如 onWindowFocusChanged() 回调方法

   ```java
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

   ```java
   @Override
   protected void onResume() {
       super.onResume();
       view1.postDelayed(new Runnable() {
           @Override
           public void run() {
               // doing
           }
       }, 300);
   ```

4. 在onCreate()或onResume()等方法中需要获取宽高时使用getViewTreeObserver().addOnGlobalLayoutListener()
   来为view添加回调在回调里获得宽度或者高度获取完后让view删除该回调

   ```java
   ViewTreeObserver() observer = view1.getViewTreeObserver()；
   observer.addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
           @Override
           public void onGlobalLayout() {
               view1.getViewTreeObserver().removeGlobalOnLayoutListener(this);
               int width = view.getMeasureWidth();
           }
       });
   ```

   注意：

   当View树的状态发生改变或者view的可见性发生改变时onGlobalLayout方法会被回调；

   所以，伴随着view树的改变，此方法会被回调多次

   

5. view.measure(int widthMeasureSpec,int heightMeasureSpec)
   手动对view进行measure来得到view的宽高



