1. 如果ListView没有HeaderView或者FooterView的时候，与ListView相关联的Adapter就是传进来的参数Adapter。如果有，则原来的Adapter将被包装成HeaderViewListAdapter，通过getWrappedAdapter()方法可以获取原来的Adapter，它所在的list不是原来的注Adapter的list,其size可能为0，,如果操作不当，例如使用list.remove(item);和notifyDataSetChanged();可能会造成

java.lang.IndexOutOfBoundsException: Invalid index 0, size is 0
at java.util.ArrayList.throwIndexOutOfBoundsException(ArrayList.java:255)
at java.util.ArrayList.get(ArrayList.java:308)
at android.widget.HeaderViewListAdapter.isEnabled(HeaderViewListAdapter.java:164)
at android.widget.AbsListView.setItemViewLayoutParams(AbsListView.java:2492)...

数组下标越界的情况，从崩溃日志不能直接找到我们写的代码是那一行导致的



2. 使用ListView时如果用到removeHeaderView，一定要确定ListView已经使用了setAdapter方法，不然会报NullPointException，addFooterView必须在setAdapter之前才会生效。



3.使用ListView时如果要隐藏HeaderView，可以通过removeHeaderView来实现，也可以把headerView设为gone，然后headerView.setPadding(-headerView.height）来实现。



4.Activity保存状态信息是应该在onPause时做，而不是onStop时做，以为可能因为内存紧张，可能不会调用onStop方法就已经被回收。



5. 在自定义一个UI控件的时候，一定要提供一个具有两个参数类型分别为Context和AttributeSet的构造函数，否则的话，该自定义控件就不可以在UI布局文件中使用。



6.在定义Dialog，调用其dismiss和show方法的时候，一定要注意判断调用该Dialog的activity是不是已经为空或者已经finish了。



7.用SparseArray<E>代替HashMap能提高性能。



8.当使用.9图做为一个view的background，如果在代码中动态修改了它的background，那么，这个view原先设置的padding将会失效。应先保存去padding值，然后等动态设置完background后再通过setPadding设置padding值。



9.可以把ListView的adapter设置为null，这样就只显示ListView的headView



10.ListView的adapter是通过一个list关联其item的，如果子线程会动态修改这个list（即子线程和adapter引用同一个对象，子线程会修改这个对象的值），在滑动ListView的时候就会有异常抛出



11.如何让EditText不自动获取焦点？在EditText的父Layout中,加入下面的两个属性即可 : android:focusable="true"，android:focusableInTouchMode="true"。



12.数据库加，减字段，注意数据库版本升级，否则会导致崩溃



13.尽量使用ApplicationContext，activity的context有可能被回收，getActivity也要进行判断是否为空



14.在activity中通过getWindow().setBackgroundDrawable(null);可以减少一个层级。（getWindow().setBackgroundDrawable()还有另外一个用法就是输入法弹下去时背景为黑色，可以通过这个来改为想要的颜色）



15.隐藏ListView的footerView可以通过设gone和设padding(0, -footerviewHeight, 0, 0)来搞定。



16.通过getWindow().setBackgroundDrawable(null);把window的背景设置为null，同时没有设置layout的背景，滑动的时候会导致花屏



17.在Adapter的getView()方法中，对convertView通过setLayoutParams(width, height)方法设置在父View中的width和height时，height为0，是没有作用的，设置为1就有作用。同时MATCH_PARENT为-1，WRAP_CONTENT为-2,也要注意使用。



18.ListView使用方法setSelection(position)不起作用是因为有其他地方在操作UI



19.Android中animation自从开始起作用后，就缓存到了某个地方，只管不停的绘制，哪怕自己都不存在了，都还在那绘制，clearAnimation的作用就是通知一下他，你都没了，别再画了（比如一个button，startAnimation后没有clearAnimation，你点击的话很难相应，那就是因为这个button一直在不停的绘制，你点击的时候一直获取不到焦点）。



20.Android中animation对于目标view的位置实际上是没有改变的，当android:fillAfter="true"时，动画结束后view停在动画最后一祯的位置。



21.ListView监听OnScrollListener获取的firstVisibleItem代表的是第一个完全显示的item的index



22.getChildAt(0) == getChildAt(firstVisibleItem - getHeaderViewsCount());



23.PopupWindow中的EditText点击和长按的时候是没有复制，黏贴，全选这些选项弹出来的，这是android的一个系统bug，可以使用Dialog替代PopupWindow来达到同样的效果



24.activity的finish方法中使用了synchronized (this)，所以activity的方法尽量不要使用 synchronized来修饰，或者有 synchronized (this)修饰的方法块，因为这些方法或者方法块一旦存在耗时操作，会导致finish方法无法执行，从而造成anr。



25.使用枚举通常会比使用静态常量要消耗两倍以上的内存，在Android开发当中我们应当尽可能地不使用枚举。



26.ImageView的padding只对src起作用，对background是不起作用的



27.DB中如果已经创建了一张表，如果没有删除这张表，直接再以同样的名字创建表，则会抛异常。



28.

activity和service，静态broadcast receiver关于android:export属性的默认值，如果有intent filter，则为true，没有intent filter，则为false。conent provider在api level 17及以上，android:export属性默认为false，其余为true。动态broadcast receiver，需要注意使用api，来区分android:export属性为true或者false。


对于第一种，该receiver是公开的。
对于第二种，如果broadcastPermission是签名级别的，那么是非公开的，否则就是公开的。
对于第三种，该receiver是非公开的。

29.如果在一个Activity的onResume() 中注册receiver，则必须在onPause()中注销那个receiver。目的：减少不必要的系统资源消耗

30.需要注意getActivity()可能为空；需要加入下面代码避免空指针
If（getActivity() == null || !isAdd()）{
return;
}

31.Activity或者Fragment中的BroadCastReceiver设置监听和取消监听要成对出现。onReceive方法中调用外部类要判空

32.Activity销毁之前必须先销毁附属在Activity上的Dialog；不能出现Activity销毁后，还操作Dialog对象的情况

33.使用数据库的时候要注意到数据库死锁的问题。如果数据库没有做同步处理，必须在主线程中调用db，保证db不被lock

34.打开的流资源一定要关闭，比如文件读写，数据库读写

35.Static引用的Map或者List集合要手动释放，尽量不要使用static引用集合,static修饰的变量慎用.

36.创建handler的时候要使用static内部类，否则会出现内存泄露

37.尽量少使用BroadcastReceiver，频繁使用广播会造成,考虑自己写个观察者模式或者eventBus或otto

38.能不使用Handler的情况下，尽量少使用Handler

39.使用了Handler，一定要控制一个类的范围内，不能将Handler传递到其他类中去，因为这样会破坏类的封装性

40.编程要遵循高内聚、低耦合原则。类和类之间要尽量降低耦合性，一个类尽量少的依赖其它类里的属性或者方法

41.Boolean型变量前缀使用has或者is

42.不能使用ScrollView包裹ListView/GridView/ExpandableListVIew;因为这样会把ListView的所有Item都加载到内存中，要消耗巨大的内存和cpu去绘制图面,还有可能造成嵌套的问题。

43.花括号不要单独一行，和它前面的代码同一行。而且，花括号与前面的代码之间用一个空格隔开。如：public void method() { // Good 
 
}

44.if、else、for、switch、while等逻辑关键字与后面的语句留一个空格隔开，如：// Good
if (booleanVariable) {
    // TODO while booleanVariable is true
} else {
    // TODO else
}

45.运算符两边各用一个空格隔开。如：int result = a + b; //Good, = 和 + 两边各用一个空格隔开



46.方法的每个参数之间用一个空格隔开，如：public void method(String param1, String param2); // Good，param1后面的逗号与String之间隔了一个空格



47.将逻辑相关的代码段用空行隔开，以提高可读性。空行也只空一行，不要空多行



48.一行声明一个变量，不要一行声明多个变量，这样有利于写注释。



49.以上也可以使用格式化模板（规范的），导入开发工具中，然后在提交代码的时候格式化



50.应用中的字符串统一在strings.xml中定义，然后在代码和布局文件中引用。



51.颜色值统一在colors.xml中定义，然后在代码和布局文件中引用。另外，不要在代码和布局文件中引用系统的颜色，除了透明。



52.在变量名中使用互补对，如min/max、begin/end和open/close。



53.服务端可以实现的，就不要放在客户端



54.引用第三方库要慎重，避免应用大容量的第三方库，导致客户端包非常大



55.使用静态变量方式实现界面间共享要慎重



56.不要重用父类的handler，对应一个类的handler也不应该让其子类用到，否则会导致message.what冲突



57.全局变量放全局类中，模块私有放自己的管理类中，让常量清晰且集中.



58.如果数据没有必要加载，数据请务必延迟初始化，谨记为用户节省内存，总不会有坏处。



59.异常抛出，在合适的位置处理或者集中处理，不要搞的到处是catch，混乱且性能低，尽量不要在循环体中捕获异常，以提升性能。



60. 地址引用链长时(3个以上指向)小心内存泄漏，和警惕堆栈地址指向，典型的易发事件是：数据更新了，ListView视图却没有刷新，这时Adapter很可能指向并的并不是你更新的数据容器地址(一般为List)。



61.多线程操作数据库时，db关闭了会报错，也很可能出现互锁的问题，推荐使用事务



62.做之前先考虑那些可以公用，资源，layout，类，做一个结构、架构分析以加快开发，提升代码可复用度。



63.数据库删除数据时，要注意级联操作避免出现永远删不掉的脏数据喔。



64.listview在数据未满一屏时，setSelection函数不起作用;ListView批量操作时各子项和视图正确对应，可见即所选。



65.码块/常量/资源可以集中公用的一定共用，即使共用逻辑稍复杂一点也会值得，修改起来很轻松，修改一种，到处有效。



66.setSelection不起作用，尝试smoothScrollToPosition。ListView的LastVisiblePosition(最后一个可见子项)会随着getView方法执行位置不同变动而变。



67.与Activity通讯使用Handler更方便; 如果你的框架回调链变长，考虑监听者模式简化回调。



68.Handler在子线程线程使用Looper.prepare，或者new的时候给构造函数传入MainLooper来确保在主线程run。



69.构造函数里面，Adapter极度不推荐启动异步线程，会埋下隐患



70.千万不要理所当然的以为一个对象不会为空，充分的做好容错处理;另外注意null也可以插入ArrayList等容器中。



71.ExpandableListView的子列表不能点击(禁用)要把Adapter的isChildSelectable方法返回true。



72.存在多个不同的dbhelper实例情况下，sqlitedatabase对象必然存在不同的实例，多线程同时写入数据，轮流写入数据时会不定时的报db is locked，引起崩溃，不管是操作同张表还是异表。读和写可以同时并发，轮流无规律的交替执行。同时写入数据时解决方案是用并发的每个线程都用事务，db则不会lock，按次整体写入



73.建议整个应用维护一个dbhelper实例，只要db没有关闭，全局就只有一个db实例，多线程并发写入db不会lock



74.经常需要用ListView或者其它显示大量Items的控件实时跟踪或者查看信息，并且希望最新的条目可以自动滚动到可视范围内。通过设置的控件transcriptMode属性可以将Android平台的控件(支持ScrollBar)自动滑动到最底部。



75.EditText在setText时不要忘记是否需要setSelection。在大多数情况下是需要设置的



76.XML两种情况要注意：1 属性名字时候有重复;2 注意文本是否包含非法字符，注意使用CDATA包裹。



77.由于String类的immutable性质，当String变量需要经常变换其值时，应该考虑使用StringBuilder提升性能，多线程使用StringBuffer操作string提高程序效率。



78.多个RandomAccessFile对象指向同一个文件，可使用多个线程一起写入无需再自己加锁，经试验结论：三个线程分别写入100万次数据，使用锁约12秒，不使用约8.5秒。100个线程分别写入1万次数据使用锁耗时约4.2秒，不使用锁耗时约3秒。



79.XmlPullParser解析慎用nextText()方法，xml比较复杂，含有空标签、重复名字标签时容易出现异常问题;TEXT中使用getText()方法代替START_TAG中使用nextText()方法;START_TAG，TEXT，END_TAG三个事件配合使用。注意每个xml节点之间(不管是开始节点还是结束节点)都会出现TEXT事件。



80.单例模式，总结来说就有五种：懒汉、饿汉、静态内部类、枚举和双重检查锁定。我们在实现单例模式的时候往往会忽略掉多线程的情况，就是写的代码在单线程的情况下是没问题的，但是一碰到多个线程的时候，由于代码没写好，就会引发很多问题，而且这些问题都是很隐蔽和很难排查的。下面介绍比较好的双重检查加锁单例模式。

/** 双重检查加锁：
 * “双重检查加锁“的方式可以既实现线程安全，又能够使性能不受到很大的影响。
 * 那么什么是”双重检查加锁“机制呢？
 *        所谓双重检查加锁机制，指的是：并不是每次进入getInstance方法都需要同步，
 * 而是先不同步，进入方法过后，先检查实例是否存在，如果不存在才进入下面的同步块，
 * 这是第一重检查。进入同步块后，再次检查实例是否存在，如果不存在，就在同步的
 * 情况下创建一个实例，这是第二重检查。这样一来，就只需要同步一次了，从而减少了
 * 多次在同步情况下进行判断所浪费的时间。
 *       双重检查加锁机制的实现会使用一个关键字volatile，它的意思是：被volatile
 * 修饰的变量的值，将不会被本地线程缓存，所有对该变量的读写都是直接操作共享内存，从而
 *确保多个线程能正确的处理该变量。
 */


public class Singleton {

/**
*volatile：可以保证多线程下的可见性;


    *读volatile：每当子线程某一语句要用到volatile变量时，都会从主线程重新拷贝一份，这样就保证子线程的会跟主线程的一致。


     *写volatile: 每当子线程某一语句要写volatile变量时，都会在读完后同步到主线程去，这样就保证主线程的变量及时更新。
*
*
*
*/
    private static volatile Singleton uniqueInstance;


    private Singleton(){
    }


/**
*1. thread2进入#1， 这时子线程的uniqueInstance都是为空的（java内存模型会从主线程拷贝一份uniqueInstance=null到子线程thread2），thread2让出CPU资源给thread3
     * 2. thread3进入#1， 这时子线程的uniqueInstance都是为空的（java内存模型会从主线程拷贝一份uniqueInstance=null到子线程thread3）, thread3让出CPU资源给thread2
      *3. thread2会依次执行#2，#3，#4，最终在thread2里面实例化了uniqueInstance(由于是volatile修饰的变量，会马上同步到主线程的变量去)。thread2执行完毕让出CPU资源给thread3
      *4. thread3接着#1跑下去，跑到#3的时候，会又一次从主线程拷贝一份uniqueInstance！=null回来
      *5. 最后在thread3不再会重复实例化uniqueInstance了
*
*
*
*
*/
    public static Singleton getInstance(){
        if(uniqueInstance == null){ //#1
            synchronized(Singleton.class){ //#2
                if(uniqueInstance == null){ //#3
                    uniqueInstance = new Singleton(); //#4
                    
                } 
            }
        }
        return uniqueInstance;
    }
}


————————————————
版权声明：本文为CSDN博主「zhuhai0613」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/zhuhai0613/article/details/72783062
