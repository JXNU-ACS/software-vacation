# Java&Android学习 第一周
* Java
    - 基本语法 
    - 集合框架
    - 泛型
* Android
    - Activity 
    - Service
    - Material Design

---

##Java
    List集合是有序集合，集合中的元素可以重复，访问集合中的元素可以根据元素的索引来访问。    
    Set集合是无序集合，集合中的元素不可以重复，访问集合中的元素只能根据元素本身来访问（也是不能集合里元素不允许重复的原因）。
    Map集合中保存Key-value对形式的元素，访问时只能根据每项元素的key来访问其value。
```java
    迭代器遍历
    function:
            boolean hasNext();  判断迭代器是否具有后继对象
            Object next();      迭代器移动到并返回下一对象
            void remove();      删除上一次next()方法返回的对象（未调用next()则会异常）
    
        Collection a = new ArrayList();
    	a.add("123");
    	a.add("345");
    	a.add("456");
    	Iterator it = a.iterator();
    	while(it.hasNext())
    	{
    		System.out.println(it.next());
    	}
        it=a.iterator();
    	if(it.hasNext())
    	{
    		it.next();
    		it.remove();
    	}
    	System.out.println(a);
    
    Output: 123
            345
            456
            [345, 456]
            
    foreach遍历
    for(元素类型 t 元素变量 x ： 遍历对象 a）
    {
        //操作内容
    }
```

**泛型**

    对于类、方法、集合框架等可以将类型参数化，提高类型安全、减少不必要的代码。
    用尖括号来描述泛型参数

##Android
####Activity
####**生命周期**

![Activity生命周期图][1]


    Activity启动首先执行onCreat()->onStart()->onResume()，至此Activity被启动。
    当Activity被其他Activity覆盖或者系统进入锁屏状态，当前Activity被暂停（未移入后台），执行onPause(),如果是覆盖则不会调用onStop()。
    返回本Activity时，如果系统先前把处于后台的Activity销毁，再次打开则会就会执行onCreat()，否则直接执行onResume()。
    如果离开当前Activity，则先执行onPause()->onStop()，再次执行类似被暂停，会执行onCreat()\onRestart()->onStart()    （被销毁或被移入后台的状态下执行的方法）
    如果Activity需要结束，则调用onPause()->onStop()->onDestory()。
```C++
//启动程序
15:44:54 I/Luke: My activity Create
15:44:54 I/Luke: My activity Start
15:44:54 I/Luke: My activity Resume
//切换到其他Activity
15:45:01 I/Luke: My activity Pause
15:45:02 I/Luke: My activity Stop
//切换回来
15:45:10 I/Luke: My activity Restart
15:45:10 I/Luke: My activity Start
15:45:10 I/Luke: My activity Resume
//锁屏
15:46:11 I/Luke: My activity Pause
15:46:11 I/Luke: My activity Stop
//解锁
15:46:18 I/Luke: My activity Restart
15:46:18 I/Luke: My activity Start
15:46:18 I/Luke: My activity Resume
//按下Home
15:46:20 I/Luke: My activity Pause
15:46:20 I/Luke: My activity Stop
//返回
15:46:24 I/Luke: My activity Restart
15:46:24 I/Luke: My activity Start
15:46:24 I/Luke: My activity Resume
```
#####除此之外还有
**onWindowFocusChanged方法：**
>在Activity窗口获得或失去焦点时被调用，例如创建时首次呈现在用户面前；当前Activity被其他Activity覆盖；当前Activity转到其他Activity或按Home键回到主屏，自身退居后台；用户退出当前Activity。以上几种情况都会调用onWindowFocusChanged，并且当Activity被创建时是在onResume之后被调用，当Activity被覆盖或者退居后台或者当前Activity退出时，它是在onPause之后被调用

**onSaveInstanceState方法：**
>(1)在Activity被覆盖或退居后台之后，系统资源不足将其杀死，此方法会被调用；
(2)在用户改变屏幕方向时，此方法会被调用；
(3)在当前Activity跳转到其他Activity或者按Home键回到主屏，自身退居后台时，此方法会被调用。
第一种情况我们无法保证什么时候发生，系统根据资源紧张程度去调度；
第二种是屏幕翻转方向时，系统先销毁当前的Activity，然后再重建一个新的，调用此方法时，我们可以保存一些临时数据；
第三种情况系统调用此方法是为了保存当前窗口各个View组件的状态。onSaveInstanceState的调用顺序是在onPause之前。

**onRestoreInstanceState方法：**
>(1)在Activity被覆盖或退居后台之后，系统资源不足将其杀死，然后用户又回到了此Activity，此方法会被调用；
(2)在用户改变屏幕方向时，重建的过程中，此方法会被调用。我们可以重写此方法，以便可以恢复一些临时数据。onRestoreInstanceState的调用顺序是在onStart之后。

####**启动方式**
    在AndroidManifest.xml对应的<activity>中填写android:launchMode:=""
**standard**

    默认模式，默认模式，可以不用写配置。在这个模式下，都会默认创建一个新的实例。因此，在这种模式下，可以有多个相同的实例，也允许多个相同Activity叠加。
    例如：
    若我有一个Activity名为A1, 上面有一个按钮可跳转到A1。那么如果我点击按钮，便会新启一个Activity A1叠在刚才的A1之上，再点击，又会再新启一个在它之上……
    点back键会依照栈顺序依次退出。
**singleTop**

    可以有多个实例，但是不允许多个相同Activity叠加。即，如果Activity在栈顶的时候，启动相同的Activity，不会创建新的实例，而会调用其onNewIntent方法。
    例如：
    若我有两个Activity名为B1,B2,两个Activity内容功能完全相同，都有两个按钮可以跳到B1或者B2，唯一不同的是B1为standard，B2为singleTop。
    若我意图打开的顺序为B1->B2->B2，则实际打开的顺序为B1->B2（后一次意图打开B2，实际只调用了前一个的onNewIntent方法）
    若我意图打开的顺序为B1->B2->B1->B2，则实际打开的顺序与意图的一致，为B1->B2->B1->B2。
**singleTask**

    只有一个实例。在同一个应用程序中启动他的时候，若Activity不存在，则会在当前task创建一个新的实例，若存在，则会把task中在其之上的其它Activity destory掉并调用它的onNewIntent方法。
    如果是在别的应用程序中启动它，则会新建一个task，并在该task中启动这个Activity，singleTask允许别的Activity与其在一个task中共存，也就是说，如果我在这个singleTask的实例中再打开新的Activity，这个新的Activity还是会在singleTask的实例的task中。
    例如：
    若我的应用程序中有三个Activity,C1,C2,C3，三个Activity可互相启动，其中C2为singleTask模式，那么，无论我在这个程序中如何点击启动，如：C1->C2->C3->C2->C3->C1-C2，C1,C3可能存在多个实例，但是C2只会存在一个，并且这三个Activity都在同一个task里面。
    但是C1->C2->C3->C2->C3->C1-C2，这样的操作过程实际应该是如下这样的，因为singleTask会把task中在其之上的其它Activity destory掉。
    操作：C1->C2          C1->C2->C3          C1->C2->C3->C2            C1->C2->C3->C2->C3->C1             C1->C2->C3->C2->C3->C1-C2
    实际：C1->C2          C1->C2->C3          C1->C2                              C1->C2->C3->C1                               C1->C2
    若是别的应用程序打开C2，则会新启一个task。
    如别的应用Other中有一个activity，taskId为200，从它打开C2，则C2的taskIdI不会为200，例如C2的taskId为201，那么再从C2打开C1、C3，则C2、C3的taskId仍为201。
    注意：如果此时你点击home，然后再打开Other，发现这时显示的肯定会是Other应用中的内容，而不会是我们应用中的C1 C2 C3中的其中一个。
**singleInstance**

    只有一个实例，并且这个实例独立运行在一个task中，这个task只有这个实例，不允许有别的Activity存在。
    例如：
    程序有三个ActivityD1,D2,D3，三个Activity可互相启动，其中D2为singleInstance模式。那么程序从D1开始运行，假设D1的taskId为200，那么从D1启动D2时，D2会新启动一个task，即D2与D1不在一个task中运行。假设D2的taskId为201，再从D2启动D3时，D3的taskId为200，也就是说它被压到了D1启动的任务栈中。
    若是在别的应用程序打开D2，假设Other的taskId为200，打开D2，D2会新建一个task运行，假设它的taskId为201，那么如果这时再从D2启动D1或者D3，则又会再创建一个task，因此，若操作步骤为other->D2->D1，这过程就涉及到了3个task了。

####Service
####**生命周期**
![Service生命周期及启动方式][2]
**startService**

    startService()创建->onCreate->onStartCommand()->stopSelf或其他组件调用stopService()结束->onDestroy()
    多次调用startService()不会创建多个Service但多多次执行onStart()。
**bindService**

    bindService()绑定activity与service->onBind()->onUnbind()->onDestroy()
    这两条路径并不是完全分开的。
    即是说，你可以和一个已经调用了 startService()而被开启的service进行绑定。
    比如，一个后台音乐service可能因调用 startService()方法而被开启了，稍后，可能用户想要控制播放器或者得到一些当前歌曲的信息，可以通过bindService()将一个activity和service绑定。这种情况下，stopService()或 stopSelf()实际上并不能停止这个service，除非所有的客户都解除绑定。

**The entire lifetime**

    service整体的生命时间是从onCreate()被调用开始，到onDestroy()方法返回为止。
    和activity一样，service在onCreate()中进行它的初始化工作，在onDestroy()中释放残留的资源。
    比如，一个音乐播放service可以在onCreate()中创建播放音乐的线程，在onDestory()中停止这个线程。
    onCreate() 和 onDestroy()会被所有的service调用，不论service是通过startService()还是bindService()建立。
**The active lifetime**

    service积极活动的生命时间（active lifetime）是从onStartCommand() 或onBind()被调用开始，它们各自处理由startService()或 bindService()方法传过来的Intent对象。
    如果service是被开启的，那么它的活动生命周期和整个生命周期一同结束。
    如果service是被绑定的，它们它的活动生命周期是在onUnbind()方法返回后结束。
    注意：尽管一个被开启的service是通过调用 stopSelf() 或 stopService()来停止的，没有一个对应的回调函数与之对应，即没有onStop()回调方法。所以，当调用了停止的方法，除非这个service和客户组件绑定，否则系统将会直接销毁它，onDestory()方法会被调用，并且是这个时候唯一会被调用的回调方法。
**Managing the Lifecycle of a Bound Service**

    当绑定service和所有客户端解除绑定之后，Android系统将会销毁它，（除非它同时被onStartCommand()方法开启）。
    因此，如果你的service是一个纯粹的绑定service，那么你不需要管理它的生命周期。
    然而，如果你选择实现onStartCommand()回调方法，那么你必须显式地停止service，因为service此时被看做是开启的。
    这种情况下，service会一直运行到它自己调用 stopSelf()或另一个组件调用stopService()，不论它是否和客户端绑定。
    另外，如果你的service被开启并且接受绑定，那么当系统调用你的 onUnbind()方法时，如果你想要在下次客户端绑定的时候接受一个onRebind()的调用（而不是调用 onBind()），你可以选择在 onUnbind()中返回true。
    onRebind()的返回值为void，但是客户端仍然在它的 onServiceConnected()回调方法中得到 IBinder 对象。
    下图展示了这种service（被开启，还允许绑定）的生命周期：
![service（被开启，还允许绑定）的生命周期][3]
    
##参考资料
>http://blog.csdn.net/liuhe688/article/details/6733407
 http://blog.csdn.net/shinay/article/details/7898492/


  [1]: http://hi.csdn.net/attachment/201109/1/0_1314838777He6C.gif
  [2]: http://images.cnitblog.com/blog/325852/201303/24233205-ccefbc4a326048d79b111d05d1f8ff03.png
  [3]: http://images.cnitblog.com/blog/325852/201303/24233647-0a03689c14da415ebbcb2913f2932d7d.png