![image](http://upload-images.jianshu.io/upload_images/1479978-a672e55cc06a5585.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

生命周期：
1. onCreate
2. onStart  
   Activity正在被启动 此时Activity**可见但还没有出现在前台不可和用户交互**
3. onResume  
   Actvity可见且**出现在前台可以和用户交互**
4. onPause  
   变为不可见，因为它更容易被触，所以可以在此生命周期方法中保存数据，但是不宜做耗时操作，因为只有先执行完旧Actvity的onPause后 新Actvity才会启动
5. onStop
6. onRestart
7. onDestory


**ActivityLifecycleCallbacks**   
ActivityLifecycleCallbacks 是 Application 中的一个内部接口：  
Application 提供有一个 `registerActivityLifecycleCallbacks()` 的方法,需要传入的参数就是这个 ActivityLifecycleCallbacks 接口,系统会在每个 Activity 执行完对应的生命周期后都调用这个实现类中对应的方法,请记住是每个!  
使用场景：  


##### 几种场景下的生命周期：

1. ActivityA第一次启动执行的生命周期方法  
onCrteate --> onStart --> onResume
2. 打开新的ActivityB或切换到桌面执行的生命周期方法：  
A.onPause --> B.onCreate --> B.onStart --> B.onRersume --> A.onStop  
这里有几个点需要注意下：
- 如果B是透明主题 或 如果B是对话框，那么A.stop就不会被调用
- 如果A 添加了`<item name="android:windowIsTranslucent">true</item>`主题属性，那么A.stope也不会被调用
3. 锁定屏幕与解锁屏幕   
有的只会调用 onPause 有的会调用onPause和onStop；那么当开屏后的生命周期对应的分别是 onResume 或者onStart-->onResume
4. Activity异常终止时系统会调用onSaveInstanceState和onRestoreInstanceState来存储和恢复数据
5. 横竖屏切换时候activity的生命周期
- 不设置Activity的`android:configChanges`时，切屏会重新调用各个生命周期，切横屏时会执行一次，切竖屏时会执行两次
- 设置Activity的`android:configChanges="orientation"`时，切屏还是会重新调用各个生命周期，切横、竖屏时只会执行一次
- 设置Activity的`android:configChanges="orientation|keyboardHidden"`时，切屏不会重新调用各个生命周期，只会执行onConfigurationChanged方法

##### Activity中一些重要方法
1. startActivity  startActivityForResult  onActivityResult setResult
2. onSaveInstanceState / onRestoreInstanceState
3. onNewIntent  
当前通过Intent方式启动的Activity是复用之前已经存在的实例时，此时都会执行到onNewIntent，onNewIntent调用后，将继续回调onRestart，onStart，onResume.
4. onBackPressed  
重写按下Back键的回调函数onBackPressed，转成Home键的效果，这样可以使应用回到桌面，下次打开无需重新create

```
 @Override
    public void onBackPressed() {
        Intent home = new Intent(Intent.ACTION_MAIN);
        home.addCategory(Intent.CATEGORY_HOME);
        startActivity(home);
    }
```
5. moveTaskToBack(true)  
直接将当前Activity所在的Task移到后台，同时保留activity顺序和状态

 

##### Activity被回收数据保存和恢复代码
```
public class AllAppActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        //判断是否有以前的保存状态信息
        if(savedInstanceState!=null){ 
            savedInstanceState.get("key");
        }
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
    @Override
    protected void onSaveInstanceState(Bundle outState) {
        //可能被回收内存前保存状态和信息，
        Bundle data = new Bundle();
        data.putString("key", "value");
        outState.putAll(data);
        super.onSaveInstanceState(outState);
    }
    @Override
    protected void onRestoreInstanceState(Bundle savedInstanceState) {
        //判断是否有以前的保存状态信息
        if(savedInstanceState!=null){ 
            savedInstanceState.get("key");
        }
        super.onRestoreInstanceState(savedInstanceState);
    }
}
```







