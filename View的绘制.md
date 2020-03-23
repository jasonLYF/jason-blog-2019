### View的绘制

![1734948-b4493f7b0234dd69.jpg](http://note.youdao.com/yws/res/5702/WEBRESOURCEbe7f3f2b1e00e3d2e1702768d7f0636f)

View的绘制流程是从ViewRoot的performTraversals方法开始，performTraversals会依次调用performMeasure，performLayout，performDraw，这三个方法分别完成View的measure，layout，draw三个流程；

### 第一步：measure

performMeasure会调用measure方法，而measure方法又回调用onMeasure，在onMeasure中完成对View的测量；

measure决定了View的宽和高，measure完成后那，可以通过getMeasureWidth和getMeasureHeight来获取View测量后的宽高，通常情况下它都等于View最终的宽高；

##### getMeasureWidth 与 getWidth 的区别？

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

### 第二步：layout

layout决定了View四个顶点的坐标和View实际的宽高，layout阶段决定了view的最终大小，layout完成后可以通过getTop、getLeft等来获取View四个顶点的坐标，并可通过getWidth和getHeight来获取View最终的宽和高；

##### onLayout()
调用onLayout()的时机是：View需要给自己设置大小和位置了或者ViewGroup需要给子View和ViewGroup自身时调用。

### 第三步：draw

draw方法决定了VIew的显示，只有draw完成后view的内容才能呈现在屏幕上；

View的绘制分为以下几个步骤：绘制背景、绘制自己、绘制子view、绘制装饰

##### drawChild()
去重新回调每个子视图的draw()方法

##### requestLayout
调用requestLayout()方法的时机是：当前View发生了一些改变，这个改变使得现有的View失效，所以调用requestLayout()方法对View树进行重新布局，过程包括了measure()和layout()过程，但不会调用draw()过程，即不会发生重新绘制视图过程。

##### invalidate() & postInvalidate
只执行自身draw方法  
invalidate:在ui线程刷新view；  
postInvalidate：在工作线程刷新view（底层还是handler）


