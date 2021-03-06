##打开一个Activity

android系统不像其他的编程应用一样用main()方法开始，而是在activity中通过activity的各个生命周期回调方法来初始化代码.

###理解Activity生命周期回调方法
在Activity的生命周期中，系统调用一系列核心生命周期方法一步步走向一个金字塔顶端，Activity生命周期每一个回调都相当于朝向金字塔的一步，随着系统创造一个activity示例，每个回调方法都使得activity走向金字塔顶端更近一步，金字塔顶端就是activity运行在前台可以与用户交互的状态，随着用户离开Activity，系统调用其他的生命周期回调方法慢慢走下金字塔，某些情况下，activity会在下来一两步后停下等待（比如当用户切换到其他的app中），这种情况activity有可能回到金字塔顶端（如果用户切回到当前activity），并且activity恢复到离开activity时的样子
![Activity生命周期示意图，摘自AndroidAPI文档](http://img.blog.csdn.net/20170228112404018?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2luYXRfMzU4MTU2NzY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
根据你自己的activity的复杂度，你可以不必需要重写所有生命周期方法，但是你必须理解每一个生命周期回调方法的重要性以确保你的app的体验和所期望的一致，重写生命周期方法使得你的app用户体验更好，主要体现在以下几个方面：

    1.当用户在使用你的app的时候收到电话或者用户切换到别的app的时候不会崩溃.
    2.当用户不是正在使用你的app的时候不浪费有价值的系统资源.
    3.如果用户不离开你的app再返回你的app的时候不关闭用户的使用进度.
    4.当屏幕在landscape和portrait之间(横竖屏)切换的时候不会崩溃或失去用户的进度.
在接下来的学习中我们将知道，Activity在各种不同的状态之间切换的时候有好几种情形。然而，只有三种状态是静态的，也就是说，activity可以在这三个状态可以长时间停留。
	
	Resumed(恢复/运行状态)
	    在这种状态，activity在前台并且可以和用户交互(有时也称作“运行”状态)
	Paused(暂停状态)
	    在这种状态，activity是被另一个activity挡住一部分的，这挡住它的activity是半透明的或者没有覆盖整个屏幕，暂停的activity不能接收用户的指令也不能执行任何代码.
	Stopped(停止状态)
	    在这种状态，activity完全被隐藏，且对用户是不可见的，它被认为是在后台运行，当进入stopped状态时，activity实例和所有的状态信息比如成员变量都被保存起来了，但是它也不能执行任何代码. 
其他的状态(Created 和Started)都是很短暂的状态，系统很快的通过调用生命周期的方法来从他们跳转到下一个状态，系统调用onCreate之后，会马上调用onStart, 紧接着就是onResume了.以上就是基本的activity生命周期，现在我们将来学习特殊的生命周期行为了.
###指定App的默认启动Activity
#####当用户在主屏上选择你的app图标，系统调用在你的app中定义的默认启动的activity的onCreate方法，这个activity是你的app用户界面的主要入口，你可以在AndroidManifest.xml定义你的程序入口activity是哪个activity. 具体定义如下
	<activity android:name=".MainActivity" android:label="@string/app_name">
	    <intent-filter>
	        <action android:name="android.intent.action.MAIN" />
	        <category android:name="android.intent.category.LAUNCHER" />
	    </intent-filter>
	</activity>
注意：在新建的android项目中默认有一个activity是app的入口activity. 如果你的所有的activity都是要么没有Main action或者LAUNCHER category,或者两者都没有，那么你的app图标将不会出现在在主屏app列表中.
###创造一个实例

大多数包含多个activity的app允许用户表现不同的action，无论你的activity是程序的主入口(点击app图标进入的activity)还是回应用户action打开的activity，系统将通过onCreate()方法创造这个activity的实例. 

你必须重写onCreate方法来执行基本的应用启动逻辑，这只在activity的生命周期中执行一次，例如，你重写onCreate方法应该定义用户界面和实例化类范围内的变量.

例如，接下来的onCreate()方法的实例向我们展示了执行基本的activity启动的一些代码，比如定义用户界面（xml布局文件中定义的），定义成员变量，配置一部分UI.

	TextView mTextView; // Member variable for text view in the layout
	
	@Override
	public void onCreate(Bundle savedInstanceState) {
	    super.onCreate(savedInstanceState);
	
	    // Set the user interface layout for this Activity
	    // The layout file is defined in the project res/layout/main_activity.xml file
	    setContentView(R.layout.main_activity);
	    
	    // Initialize member TextView so we can manipulate it later
	    mTextView = (TextView) findViewById(R.id.text_message);
	    
	    // Make sure we're running on Honeycomb or higher to use ActionBar APIs
	    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
	        // For the main activity, make sure the app icon in the action bar
	        // does not behave as a button
	        ActionBar actionBar = getActionBar();
	        actionBar.setHomeButtonEnabled(false);
	    }
	}
注意：使用SDK_INT来防止旧版本的系统执行新版本系统的代码，这部分代码在Android 2.0 (sdk = 5)及以上是好使的，旧版本系统执行这段代码会出现runtime异常.
####一旦onCreate()方法执行完毕，系统将马上调用onStart()和onResume(),你的Activity绝不会在Created或者Started状态下停留，理论上说，activity在onStart()被调用后是可视的，但onResume()方法会马上跟着执行且actvity会停留在Resumed状态除非有东西或者事件改变了它，比如来电话了，用户打开了另外一个activity或者屏幕关闭了.

####在接来的课程中,你将会看到其他的生命周期方法，onStart()和onResume()方法在当你的activity的状态需要从Paused或者Stopped状态中恢复的时候的作用。 
####注意：onCreate()方法包含一个参数savedInstanceState，我们将在后面的课程中关于重建一个activity的时候来讨论.
![基本的生命周期示意图，摘自Android文档](http://img.blog.csdn.net/20170228130645112?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2luYXRfMzU4MTU2NzY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
###摧毁一个Activity
####一个activity的第一个生命周期函数是onCreate(), 最后一个生命周期函数是onDestory(), 系统会在你的activity实例要被完全从系统内存中移除时调用该方法.


####大多数app不需要重写这个方法因为局部的类引用将与activity同时被销毁，你的activity 应该在onPause()或者onStop()方法中执行清理操作，然而如果你的activity在onCreate()方法创建了一个后台线程或者长时间的资源占用可能在没有合适的关闭下会造成内存泄漏，你应该在onDestory()方法中杀死它们.
	@Override
	public void onDestroy() {
	    super.onDestroy();  // Always call the superclass
	    
	    // Stop method tracing that the activity started during onCreate()
	    android.os.Debug.stopMethodTracing();
	}
注意：系统只能在调用onPause()和onStop()后才会调用onDestory(),除非你在onCreate方法中调用了finish()方法，在某些情况下，比如你的activity决定启动另一个activity ，你可能会在onCreate()方法中调用finish() 然后销毁activity，在这种情况下，系统将会马上调用onDestory()而不调用其他的生命周期方法.


##暂停和恢复/继续一个Activity


在正常的app使用过程中，前台activity有时会被另外的可视化组件阻塞，导致activity进入Paused状态，例如,当一个半透明的activity打开的时候(比如dialog样式的activity)，先前的activity暂停了，只要这个activity是部分可视的但却没有获取到焦点 它就是paused状态.

然而,只要这个这个activity被完全的阻塞了并且不能被看见，它将进入stoped状态(下节课中我们将讨论).

由于你activity进入了paused状态，系统调用你的activity中的onPause()方法，它允许你停止在暂停状态下不该继续的动作，或者保留一些应该被永久保留以防止用户离开你的app的信息，这样当你的用户从paused状态回来的时候，系统将恢复它并且调用onResume方法.

注意：当你的app收到要调用onPause方法时，这可能是表示你的activity将会进入onPause状态一段时间并且用户可能会某一时刻返回到你的activity，然而,这通常代表着用户要离开你的activity了.
###暂停你的activity

当系统调用你的activity的onPause()方法时，理论上意味着你的activity还是部分可见的，但大多数通常是表示用户离开你的activity了，并且通常很快进入stopped状态你应该尝尝使用onPause()回调来:

    停止动画或者其他正在继续消耗CPU的行为.
    提交没有保存的改变，只在用户在离开的时候期望这些改变是永久保存的情况下执行(比如邮箱草稿).
    释放系统资源，比如广播接收器，传感器(例如GPS)或者任意其他在activity是暂停状态下且用户不需要的影响电池寿命的资源文件.

例如，如果你的应用使用了照相机，onPause()方法将是一个好的地方来释放资源.

	@Override
	public void onPause() {
	    super.onPause();  // Always call the superclass method first
	
	    // Release the Camera because we don't need it when paused
	    // and other activities might need to use it.
	    if (mCamera != null) {
	        mCamera.release()
	        mCamera = null;
	    }
	}
一般来说，你不应该在onPause()中永久存储这些改变（比如表单中的个人信息），只有当你很确信在onPause()中这些用户期望做出的改变自动需要永久保存起来时（比如邮件草稿）你才能这么做. 然而，你应该尽量避免CPU在onPause()中高强度工作，比如写入数据到数据库，因为这将减慢activity跳转到下一个activity的可视化的转变(你应该把这些高强度的运算放在onStop()中执行).

你应该使得在onPause()中的计算相当简单以使得如果你的activity要被stop的时候，用户的跳转到下一个activity的速度很快.

注意:当你的activity被暂停了, Activity实例确是一直存在于内存之中，并且当activity回复的时候会被再次调用. 你不需要再次实例化已经在曾走向Resumed状态被调用的生命周期的方法中被创建的组件.
###恢复你的activity

当用户从Paused状态恢复时，系统将调用onResume()方法.

我们注意到系统在每次进入前台用户视野的时候都会掉用这个方法，包括首次创建的时候，这样我们应该重写onResume()来初始化在onPause()中被释放的组件并执行任何其他的必须activity进入Resumed状态需要初始化的变量的初始化(比如，开始动画和只在activity获取到用户焦点时候初始化的组件).

下面onResume()的例子 上面onPause() 例子的相对方法,所以它初始化了在activity进入暂停状态下释放的照相机资源 .

	@Override
	public void onResume() {
	    super.onResume();  // Always call the superclass method first
	
	    // Get the Camera instance as the activity achieves full user focus
	    if (mCamera == null) {
	        initializeCamera(); // Local method to handle camera init
	    }
	}
##停止并重新开始Activity
合适的停止和重新开始activity一个在activity生命周期中重要的过程，它确保了你的用户知道你的app一直是活着的么而且没有丢失他们的进度，有这样几个关键的场景（activity停止并重新开始）：

    用户打开了最近使用的app窗口并且从你的app切换到另一个app，你的app中的activity目前就处于前台并且是停止状态. 如果用户从主屏图标或者最近使用的app列表中回到你的app，那么这个activity将会restart.
    用户在你的app中打开了一个新的activity，则当前的activity会在第二个activity创建的时候停止. 如果用户在第二个activity页面按了返回按钮，泽第一个activity会restart.
Antivity提供了2个生命周期方法，onStop() 和 onRestart(),这两个方法允许你具体的处理你的activity在变为stopped和restarted状态的过程. 不像表示部分UI被挡住的paused状态，stopped状态保证了UI不再是可视的并且用户耳朵焦点已经在另一个activity上（或者别的app上）.

注意：因为系统在activity处于stopped状态时保持我们的Activity实例在内存中，所以很可能你不需要重写onStop()和onRestart()方法（甚至是onStart()方法也不需要重写），对大多数activity来说 他们都是相对比较简单的，activity将会stop并restart刚刚好，你也许只需要使用onPause()来暂停正在执行的动作和与系统资源断开连接。

###停止你的activity
当你的activity收到onStop()的回调方法，它就不再是可视的了，并且它应该释放在用户不再使用的时候不再需要的绝大多数资源，一旦你的activity停止了系统也许会在恢复系统内存的时候会销毁实例，在极端情况下，系统也许会简单的杀死你的app进程而不调用你的activity的onDestory()方法 ,素以使用onStop()来释放资源是防止内存泄漏的重要的手段.

尽管onPause() 方法在onStop()之前调用, 你还是应该使用onStop()来执行更大的更密集型的cpu运算 ,比如把信息写入数据库.
 
 例如，这里重写了onStop()方法保存了便条草稿的内容能够到本地的存储：
	
	@Override
	protected void onStop() {
	    super.onStop();  // Always call the superclass method first
	
	    // Save the note's current draft, because the activity is stopping
	    // and we want to be sure the current note progress isn't lost.
	    ContentValues values = new ContentValues();
	    values.put(NotePad.Notes.COLUMN_NAME_NOTE, getCurrentNoteText());
	    values.put(NotePad.Notes.COLUMN_NAME_TITLE, getCurrentNoteTitle());
	
	    getContentResolver().update(
	            mUri,    // The URI for the note to update.
	            values,  // The map of column names and new values to apply to them.
	            null,    // No SELECT criteria are used.
	            null     // No WHERE columns are used.
	            );
	}
当你的activity进入了stopped状态，activity对象仍然在内存中，他将会在activity恢复的时候被调用，你不需要再次初始化已经初始化过了的组件，系统也一直追踪布局文件中每个View上午当前的状态，所有如果用户在EditText组件中输入了一段文本，这些内容会被记录起来，所以你不必担心有没有必要去保存或者修复它.

注意：即便当activity处于stopped状态，系统销毁了activity，它仍然保持着View对象的状态（比如EditText中的文本），并把它存储到Bundle（键值对）中，并且在用户打开相同的实例的时候恢复他们(下一节我们讲关于使用Bundle保存数据以防activity被销毁或重建).

###开始/重新开始 Activity

当我们的actvity从stopped状态来到前台，它收到了onRestart()方法的回调，系统同时会调用onStart() 方法，onStart()方法在每次activity变成可视状态都会调用，要么在onRestart()之后执行，要么在onCreat()之后执行，然而onRestart() 方法,只有当activity从stopped状态恢复时会被调用，所以你能使用它来执行特殊的复原工作，这也许是必须的工作仅当activity先前已经被停止了但还没有被销毁的时候.

app需要使用onRestart()来存储activity状态这件事是不寻常的，所有对这个方法应用到一般的app没有任何的准则，然而因为你的onStop() 方法应该基本清除所有你的activity的资源，你将需要在acticity重新开始的时候重新实例化他们，不得不说的还有，你也需要在首次创建activity（内存中没有activity的实例）的时候实例化他们. 因为这个原因， 你应该经常使用onStart() 回调方法作为onStop()方法的对应方法, 因为系统在创建activity和restart activity的时候都会调用onStart()方法.

例如，因为用户在回到我们activity的时候可能已经离开我们的app很长一段时间了，onStart() 方法是一个好地方来验证需要的系统特性是不是能用. 

	@Override
	protected void onStart() {
	    super.onStart();  // Always call the superclass method first
	    
	    // The activity is either being restarted or started for the first time
	    // so this is where we should make sure that GPS is enabled
	    LocationManager locationManager = 
	            (LocationManager) getSystemService(Context.LOCATION_SERVICE);
	    boolean gpsEnabled = locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER);
	    
	    if (!gpsEnabled) {
	        // Create a dialog here that requests the user to enable GPS, and use an intent
	        // with the android.provider.Settings.ACTION_LOCATION_SOURCE_SETTINGS action
	        // to take the user to the Settings screen to enable GPS when they click "OK"
	    }
	}
	
	@Override
	protected void onRestart() {
	    super.onRestart();  // Always call the superclass method first
	    
	    // Activity being restarted from stopped state    
	}

当系统销毁了你的activity，它调用你的activity的onDestory方法。
因为一般来说你本应该在onStop()中释放完了大多数资源, 但如果同时收到了调用onDestroy()的命令, 这种情况一般不多见，这个方法是你最后的机会来清除资源以防止内存泄漏，所以你应该确认额外的线程已经被销毁并且像追踪类的长时间运行的行为也被停止了.

##重新创建一个Activity

有许多你的activity因为正常的app行为被销毁的场景，例如当用户按下了返回按钮或者你的activity通过调用finish()发信号销毁自己, 系统也有可能会在activity处于stopped状态并且很长一段时间没有被使用的时候销毁activity，或者前台activity需要更多的资源所以系统不得不关闭后台进程来恢复内存.

当你的activity因为用户按下返回按钮被销毁或者activity自己关闭自己，在系统看来因为这些行为导致的activity实例永远消失了，这代表着activity不再被需要了，然而，如果系统销毁因为系统约束销毁了activity（而不是正常的app行为），则尽管真实的activity实例确实消失了，系统记得它存在过，这使得如果用户再返回这个activity，系统会使用保存好的数据（activity被销毁时保存的）创建一个新的activity的实例.
这些系统把他们用来复原先前的状态的数据也被称为“实例化状态”，它是存储在Bundle对象中的键值对.

注意：你的activity将会在你旋转屏幕的时候被销毁然后重建，当你的屏幕改变了方向。系统将会销毁并重建前台activity因为屏幕特征已经改变了而且你的activity也许需要去加载对应的与原来不一样的资源（比如说layout布局）.


默认地，系统使用Bundle实例化状态来保存你的activity布局中的View对象的信息（比如EditText对象中的text文本值），所以如果你的activity实例被销毁并且重建，布局的状态被自动恢复到它先前的状态，然而，你的activity也许有更多的你想恢复的状态信息，比如activity中追踪用户进度的成员变量.

注意：为了使android系统恢复你的activity中所有view的状态，每个view必须拥有一个唯一的ID，用 android:id 这个属性来设置.

为了保存activity状态额更多的数据，你必须重写onSaveInstanceState() 回调方法. 系统在用户离开你的activity的时候调用这个方法，并把这些数据传递给Bundle对象，这样他们就会在activity意外被销毁的时候被保存起来，如果系统一定要在自之后重建activity实例，它会回传相同的Bundle 对象到onRestoreInstanceState() 和onCreate() 方法中.

###保存Activity的状态
随着你的activity开始进入stopped状态，系统调用onSaveInstanceState() 方法，所以你的activity能用键值对的方法保存状态信息. 这个方法的默认的重写保存了关于activity的View 分层信息，比如EditText中的文本信息或者ListView滚动到的位置.

为保存你的activity的更多状态信息，你必须重写onSaveInstanceState() 并增加键值对到Bundle对象. 例如:

	static final String STATE_SCORE = "playerScore";
	static final String STATE_LEVEL = "playerLevel";
	...
	
	@Override
	public void onSaveInstanceState(Bundle savedInstanceState) {
	    // Save the user's current game state
	    savedInstanceState.putInt(STATE_SCORE, mCurrentScore);
	    savedInstanceState.putInt(STATE_LEVEL, mCurrentLevel);
	    
	    // Always call the superclass so it can save the view hierarchy state
	    super.onSaveInstanceState(savedInstanceState);
	}
注意: 一定要调用父类的onSaveInstanceState() ，这样默认的view分层信息状态才会保存到Bundle对象中.

###恢复你的Activity的状态
当你的activity先前被摧毁后被重建，你可以从系统传给你的Bundle中恢复你保存的状态， onCreate() 和onRestoreInstanceState() 回调方法会收到相同的包含相同状态信息的Bundle对象.

因为onCreate() 方法在创造一个新的activity实例或者重建先前的activity中被调用. 你必须在尝试从中取值的之前检查是否这个状态Bundle是不是空的，如果是空的，则系统正在创建一个新的实例，反之则是在恢复先前被销毁的activity实例.

例如：下面是你在onCreate()中取状态数据的实例

	@Override
	protected void onCreate(Bundle savedInstanceState) {
	    super.onCreate(savedInstanceState); // Always call the superclass first
	   
	    // Check whether we're recreating a previously destroyed instance
	    if (savedInstanceState != null) {
	        // Restore value of members from saved state
	        mCurrentScore = savedInstanceState.getInt(STATE_SCORE);
	        mCurrentLevel = savedInstanceState.getInt(STATE_LEVEL);
	    } else {
	        // Probably initialize members with default values for a new instance
	    }
	    ...
	}

如果不在onCreate()方法中恢复，你也可以选择重写
onRestoreInstanceState(),这个方法将在系统调用完onStart()方法后调用，只有当至少一个保存的状态要恢复时系统才会调用onRestoreInstanceState()，所以你不需要检查Bundle是不是空的：

	public void onRestoreInstanceState(Bundle savedInstanceState) {
	    // Always call the superclass so it can restore the view hierarchy
	    super.onRestoreInstanceState(savedInstanceState);
	   
	    // Restore state members from saved instance
	    mCurrentScore = savedInstanceState.getInt(STATE_SCORE);
	    mCurrentLevel = savedInstanceState.getInt(STATE_LEVEL);
	}

注意: 一定要调用父类的 onRestoreInstanceState()才能恢复View分层状态信息.
