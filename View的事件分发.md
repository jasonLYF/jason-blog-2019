当一个MotionEvent产生后，系统需要把这个事件传递给一个具体的View，而这个传递过程就是分发过程

- dispatchTouchEvent 用来进行事件的分发

  如果事件传递给当前view，那这个方法一定会被调用 返回结果受当前view的onTouchEvent和下级view的dispatchTouchEvent方法影响 表示是否消耗当前事件

- onInterceptTouchEvent 事件拦截

- onTouchEvent 处理事件



##### 一些结论

1. 一个事件序列只能由一个View拦截或处理，一个View拦截了某个事件，同一事件序列内的所有事件都会交给这个View处理并且不再调用这个View的onInterceptTouchEvent方法；

   但是通过特殊手段可以做到，比如一个View将本该自己处理的事件通过onTouchevent强行传递给其他View

2. 如果某个View不消耗ACTION_DOWN事件(即onTouchEvent返回false)，那么同一个事件序列里的事件都不会再交给它处理，并且事件将重新交由它的父元素去处理，即父元素的onTouchEvent将会被调用

3. 如果View不消耗DOWN以为的事件，那么这个点击事件会消失，此时父元素的onTouchEvent不会被调用，并且当前View可以持续收到后续事件，最终这些消失的点击事件会传给Activity处理

4. 所有view的onTouchEvent返回false，那么Activity的onTouchEvent将会被调用

5. onClick发生的前提是当前view可以点击并且收到lDOWN和UP事件

6. 





##### 滑动冲突处理

- 外部拦截法

  重写父容器的onInterceptTouchEvent

  a、ACTION_DOWN 父容器必须返回false，即不拦截ACTION_DOWN事件；因为如果一旦拦截，那么后续的ACTION_MOVE和ACTION_UP都会直接由父容器处理，这个时候事件就没法传递给子元素了

  b、ACTION_MOVE 根据需要来决定是否拦截

  c、ACTION_UP必须返回fasle，因为这个事件本身没有意义

- 内部拦截法

  父容器不拦截任何事件 所有的事件都传递给子元素，如果子元素需要此事件就直接消耗掉，否则交由父容器处理

  需要重写子元素的dispatchTouchEvent方法

  这种处理方法需要配合requestDisallowInterceptTouchEvent方法才能正常工作

