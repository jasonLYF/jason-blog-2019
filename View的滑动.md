
### 1、让view滑动总共有几种方式，分别要注意什么？都适用于那些场景？
1. 通过view本身提供的scrollTo和scrollBy来滑动  
 
但是只能滑动view的内容 不可以滑动view本身。  
比如textview 调用这2个方法  滑动的就是显示出来的字的内容

[点击查看详情](https://www.jianshu.com/p/2b48551d5319)


2. 用动画来滑动View；
- 补间动画平移view，是对view的影像的操作，它不能真正改变view的位置参数
```
	TranslateAnimation ta = new TranslateAnimation(0, 500, 0, 200);
 	ta.setDuration(2000);
 	ta.setFillAfter(true);
	view.startAnimation(ta);
```
- 利用属性动画平移view

```
	ObjectAnimator.ofFloat(view,"translationX",0,500).setDuration(2000).start();  
	ObjectAnimator.ofFloat(view,"translationY",0,500).setDuration(2000).start(); 
```
属性动画平移view需要兼容3.0以下版本

3. 通过改变view的LayoutParams使得view重新布局从而实现滑动  

比如把一个Button向右平移100PX有下面俩种思路：
- 将Button的LayoutParams里的marginLeft参数值增加100px
- 在Button的左边放一默认宽度为0的View，当我们想想右平移Button时，重置View的宽度为100px即可，这里利用了布局占位挤压的特性

```
	ViewGroup.MarginLayoutParams params = (ViewGroup.MarginLayoutParams) view.getLayoutParams();
		params.leftMargin += 500;
```


### 2、三种滑动方式的比较
* scrollTo和scrollBy这种方式使用简单操作方便，但是它只能滑动view的内容，并不能直接滑动view本身
* 补间动画滑动，是对view影像的操作，它同样不能真正改变view的位置参数
* 属性动画滑动可以改变view的位置参数，但是需要兼容3.0以下版本
* LayoutParams这种方式，使用起来稍有麻烦，使用于有交互的view

### 3、使用动画来实现view的滑动 有什么后果？
答：实际上view动画 是对view的表面ui 也就是给用户呈现出的视觉效果 来做的移动，动画本身并不能移动view的真正位置。属性动画除外。动画播放结束以后，view最终还是会回到自己的位置的，。当然了你可以设置fillafter 属性 来让动画播放结束以后 view表象停留在 变化以后的位置。所以这会带来一个很严重的后果。比如你的button在屏幕的左边，你现在用个动画 并且设置了fillafter属性让他去了右边。你会发现 点击右边的button 没有click事件触发，但是点击左边的 却可以触发，原因就是右边的button 只是view的表象，真正的button 还在左边没有动过。你一定要这么做的话 可以提前在右边button移动后的位置放一个新的button，当你动画执行结束以后  把右边的enable 左边的让他gone就可以了。

### 4、view的滑动渐变效果有哪些方法？
三种，   
第一种是scroller 也是使用最多的。问题7里有解释。   
第二种就是动画，动画我就不多说了，不属于本文范畴。   
第三种也是我们经常使用的就是用handler ，每隔一个时间间隔 来更新view的状态。

### 5、Scroller是干嘛的？
Scroller就是用于 让view有滑动渐变效果的。

### 6、onTouchEvent和GestureDetector 在什么时候用哪个比较好？
只有滑动需求的时候 就用前者，如果有双击等这种行为的时候 就用后者。

##### View的弹性滑动
- Scroller
- 动画
- 使用延时策略（Handler，View的postDelayed、线程的sleep）



