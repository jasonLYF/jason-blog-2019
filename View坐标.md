##### View的坐标参数

top 左上角纵坐标	left左上角横坐标	bottom右下角纵坐标	right右下角横坐标

x、y是View左上角的坐标

translationX、translationY是View左上角相对于父容器的偏移量

这些坐标都是相对坐标 相对于View的父容器来说的

View平移时，top、left等是原始左上角位置信息，其值不会发生变化，此时发生改变的是x、y、translationX、translationY

getX/getY 返回的是相对于当前view左上角的x和y

getRawX/getRawY 返回的是相对于手机屏幕左上角的x和y坐标



##### View的滑动

- 通过View本身提供的scrollTo、scrollBy方法

  scrollBy内部也是调用scrollTo方法，这俩种方法只能改变View内容的位置而不能改变View在布局中的位置

- 通过动画来给View施加平移效果

  使用动画来移动view主要是操作View的translationX、translationY属性

  如果使用属性动画 需要兼容3.0以下

- 通过改变View的LayoutParams使得View重新布局而实现滑动



##### View的弹性滑动

- Scroller
- 动画
- 使用延时策略（Handler，View的postDelayed、线程的sleep）

