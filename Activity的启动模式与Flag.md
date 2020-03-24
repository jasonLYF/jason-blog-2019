
#### 启动模式

###### 1. standard 标准模式

系统默认的模式 每次启动一个Activity都会重新创建一个新的实例，不管这个实例是否存在；  
使用场景：大多数Activity。

> Application的Context不能启动Activity

由于每次启动的Activity都会运行在启动它的那个Activity所在的栈中，所以当我们用ApplicationContext去启动standard模式的Activity时，因为非Activity类型的Context没有所谓的任务栈，所以此时会报错，AndroidRuntimeException  

解决这个问题的方法是为待启动的Activity指定FLAG_ACTIVITY_NEW_TASK标记位，这样在启动此Actvity的时候就会为他创建一个新的任务栈，此时实际上是以SingleTask模式启动的Activity

###### 2. SingleTask 栈内复用模式

这是一种单例模式 只要Activity在一个栈中存在 那么多次启动都不会重新创建实例,
这种模式会回调onNewIntent  
这种模式具有clearTop的效果，会使栈内当前Activity上面的的Activity都全部出栈

 ![SingleTask](https://user-images.githubusercontent.com/20217827/67391240-dcec7f00-f5d0-11e9-8327-7a115bcd2120.png)

###### 3. SingleTop 栈顶复用模式

如果Activity已经处于任务栈顶栈顶，那么此Activity就不会重新创建 同时会回调onNewIntent方法，不会调用onCreate onStart  
使用场景：如新闻类或者阅读类App的内容页面，搜索结果页等。

###### 4. SingleInstance 单实例模式

这是一种加强的SingleTask，除了具有SingleTask的所有特性以外，另外，此种模式的Activity只能单独的位于一个栈中，并多个应用共享该栈中的该Activity实例。  
一旦该模式的Activity实例已经存在于某个栈中，任何应用再激活该Activity时都会重用该栈中的实例( 会调用实例的 onNewIntent() )  
SingleInstance不要用于中间页面，如果用于中间页面，跳转会有问题，比如：A -> B (singleInstance) -> C，完全退出后，在此启动，首先打开的是B。    
使用场景：如闹铃提醒，将闹铃提醒与闹铃设置分离


##### 如果Activity A的启动模式是SingleTask，跳转到Activity B 且返回值传给A ，要怎么处理？
Activity A使用了SingleTask模式在执行界面跳转的时候，多次启动此Activity都不会被重新创建，所以可能不会接收到传过来的Bundle里面的值，这样就导致传统的方法是接受不到返回值的。  
singleTask模式下，系统会回调onNewIntent()方法，在这个方法中可以调用 setIntent(intent);  这样就可以拿到Activity B跳到Activity A使用的Intent，从而拿到返回数据

##### Activity所需的任务栈
默认情况下，所有Activity所需的任务栈的名字为应用的包名  
我们可以单独的为每个Activity指定taskAffinity属性来指定任务栈

**同一个程序，但不同的Activity是否可以放在不同的Task任务栈中？**  
可以放在不同的Task中。需要为不同的activity设置不同的affinity属性，启动activity的Intent需要包`FLAG_ACTIVITY_NEW_TASK`标记。

#### Activity 的 Flag
**1. 指定启动模式**  
`FLAG_ACTIVITY_NEW_TASK`	为Activity指定SingleTask启动模式  
`FLAG_ACTIVITY_SINGLE_TOP`	为Activity指定SingleTop启动模式  

**2. 影响运行状态**  
`FLAG_ACTIVITY_CLEAR_TOP`	clearTop效果，同一个任务栈中所有位于它上面的Activity都出栈  	
`FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS`	不会出现在历史Activity列表里 与`android:excludeFromRecents="true"`的效果相同


#### 如何指定启动模式
1、通过 launchMode 来指定

```
<activity
    android:name=".views.MainActivity"
    android:screenOrientation="portrait"
    android:launchMode="singleTop"  指定启动模式
    android:taskAffinity="com.ryg.task1"   指定任务栈 
    />
```

2、通过在Intent中设置标志位来指定启动模

```
Intent intent = new Intent();
intent.setClass(A_Activity.this,B_Activity.class);
intent.addFlags(Intenet.FLAGS_ACTIVITY_NEW_TASK);
startIntent(intent);
```
**俩种指定启动模式的区别：**  
- 通过标志位指定的启动模式优先级高于通过launchMode指定
- 俩种方式在限定范围上有所不同：比如  
第一种方式无法为Activity设定FLAG_ACTIVITY_CLEAR_TOP标识，  
第二种方式无法为Activity指定singleInstace模式。

