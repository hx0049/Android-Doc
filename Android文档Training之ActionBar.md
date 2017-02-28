

####ActionBar 是Activity中最重要的设计元素之一，在你的app中添加ActionBar的方法如下


##只兼容Android 3.0 及以上（SDK>=11）
    
####1. Activity主题必须为Theme.Holo一类的主题
      <activity
            android:name=".HomeActivity"
            android:theme="@android:style/Theme.Holo"/>
####2. 给ActionBar添加Action
	<menu xmlns:android="http://schemas.android.com/apk/res/android" >
	    <!-- Search, should appear as action button -->
	    <item android:id="@+id/action_search"
	          android:icon="@drawable/ic_action_search"
	          android:title="@string/action_search"
	          android:showAsAction="ifRoom" />
	    <!-- Settings, should always be in the overflow -->
	    <item android:id="@+id/action_settings"
	          android:title="@string/action_settings"
	          android:showAsAction="never" />
	</menu>
#####添加菜单
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
	    // Inflate the menu items for use in the action bar
	    MenuInflater inflater = getMenuInflater();
	    inflater.inflate(R.menu.main_activity_actions, menu);
	    return super.onCreateOptionsMenu(menu);
	}
#####添加点击事件
	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
	    // Handle presses on the action bar items
	    switch (item.getItemId()) {
	        case R.id.action_search:
	            openSearch();
	            return true;
	        case R.id.action_settings:
	            openSettings();
	            return true;
	        default:
	            return super.onOptionsItemSelected(item);
	    }
	}



##兼容Android 2.1 及以上（SDK>=7）
####1. 添加支持库
	 compile 'com.android.support:appcompat-v7:25.1.0'
####2. 设置Activity集成ActionBarActivity或者设置主题为Theme.AppCompat一类的主题，即下面二选一
    public class MainActivity extends ActionBarActivity {

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	    }
	}
#####或者
    <activity android:name=".MainActivity"
    android:theme="@style/Theme.AppCompat.Light.DarkActionBar"/>
    
####3. 给ActionBar添加Action
	<menu xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:yourapp="http://schemas.android.com/apk/res-auto">
	    <!-- Search, should appear as action button -->
	    <item android:id="@+id/action_search"
	        android:icon="@mipmap/ic_launcher"
	        android:title="@string/app_name"
	        yourapp:showAsAction="ifRoom" />
	    <!-- Settings, should always be in the overflow -->
	    <item android:id="@+id/action_settings"
	        android:title="@string/app_name"
	        yourapp:showAsAction="never" />
	</menu>
####4. 剩下步骤与上面一样

##给ActionBar添加默认的返回上一页action
	<application ... >
	    ...
	    <!-- The main/home activity (it has no parent activity) -->
	    <activity
	        android:name="com.example.myfirstapp.MainActivity" ...>
	        ...
	    </activity>
	    <!-- A child of the main activity -->
	    <activity
	        android:name="com.example.myfirstapp.DisplayMessageActivity"
	        android:label="@string/title_activity_display_message"
	        android:parentActivityName="com.example.myfirstapp.MainActivity" >
	        <!-- Parent activity meta-data to support 4.0 and lower -->
	        <meta-data
	            android:name="android.support.PARENT_ACTIVITY"
	            android:value="com.example.myfirstapp.MainActivity" />
	    </activity>
	</application>

####使按钮有效
	@Override
	public void onCreate(Bundle savedInstanceState) {
	    super.onCreate(savedInstanceState);
	    setContentView(R.layout.activity_displaymessage);
	    getSupportActionBar().setDisplayHomeAsUpEnabled(true);
	    // If your minSdkVersion is 11 or higher, instead use:
	    // getActionBar().setDisplayHomeAsUpEnabled(true);
	}

##自定义ActionBar样式（android3.0及以上）
###使用系统样式
	<application android:theme="@android:style/Theme.Holo.Light" ... />
###自定义样式
####在style.xml中定义并在manifest中使用
	 <!-- the theme applied to the application or activity -->
	    <style name="CustomActionBarTheme"
	           parent="@style/Theme.Holo">
	        <item name="android:actionBarStyle">@style/MyActionBar</item>
	        <item name="android:actionBarTabTextStyle">@style/MyActionBarTabText</item>
	        <item name="android:actionMenuTextColor">@color/actionbar_text</item>
	    </style>
	
	    <!-- ActionBar styles -->
	    <style name="MyActionBar"
	           parent="@style/Widget.Holo.ActionBar">
	        <item name="android:titleTextStyle">@style/MyActionBarTitleText</item>
	    </style>
	
	    <!-- ActionBar title text -->
	    <style name="MyActionBarTitleText"
	           parent="@style/TextAppearance.Holo.Widget.ActionBar.Title">
	        <item name="android:textColor">@color/actionbar_text</item>
	    </style>
	
	    <!-- ActionBar tabs text styles -->
	    <style name="MyActionBarTabText"
	           parent="@style/Widget.Holo.ActionBar.TabText">
	        <item name="android:textColor">@color/actionbar_text</item>
	    </style>

######使用如下
	<application android:theme="@style/CustomActionBarTheme" ... />
##自定义ActionBar样式（android2.1及以上）与上面基本一样，不同点是基本Theme的设置，例如
	<!-- the theme applied to the application or activity -->
	    <style name="CustomActionBarTheme"
	           parent="@style/Theme.AppCompat">
	        <item name="android:actionBarStyle">@style/MyActionBar</item>
	        <item name="android:actionBarTabTextStyle">@style/MyActionBarTabText</item>
	        <item name="android:actionMenuTextColor">@color/actionbar_text</item>
	
	        <!-- Support library compatibility -->
	        <item name="actionBarStyle">@style/MyActionBar</item>
	        <item name="actionBarTabTextStyle">@style/MyActionBarTabText</item>
	        <item name="actionMenuTextColor">@color/actionbar_text</item>
	    </style>
	
	    <!-- ActionBar styles -->
	    <style name="MyActionBar"
	           parent="@style/Widget.AppCompat.ActionBar">
	        <item name="android:titleTextStyle">@style/MyActionBarTitleText</item>
	
	        <!-- Support library compatibility -->
	        <item name="titleTextStyle">@style/MyActionBarTitleText</item>
	    </style>
	
	    <!-- ActionBar title text -->
	    <style name="MyActionBarTitleText"
	           parent="@style/TextAppearance.AppCompat.Widget.ActionBar.Title">
	        <item name="android:textColor">@color/actionbar_text</item>
	        <!-- The textColor property is backward compatible with the Support Library -->
	    </style>
	
	    <!-- ActionBar tabs text -->
	    <style name="MyActionBarTabText"
	           parent="@style/Widget.AppCompat.ActionBar.TabText">
	        <item name="android:textColor">@color/actionbar_text</item>
	        <!-- The textColor property is backward compatible with the Support Library -->
	    </style>
##对ActionBar的简单使用
		 @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	        //android 3.0 and above
	        // ActionBar actionBar = getActionBar();
	        //android 2.1 and above
	        ActionBar actionBar = getSupportActionBar();
	        actionBar.hide();
	        //actionBar.show();
	    }
##开启ActionBar覆盖模式
####当actionbar在hide或show时会引起布局重绘浪资源，所以引进overlay mode使得actionbar一直在整个页面的最上方（设置actionbar背景半透明） 不占据屏幕位置
###android3.0及以上

    <!-- the theme applied to the application or activity -->
    <style name="CustomActionBarTheme"
           parent="@android:style/Theme.Holo">
        <item name="android:windowActionBarOverlay">true</item>
    </style>

###android2.1及以上

    <!-- the theme applied to the application or activity -->
    <style name="CustomActionBarTheme"
           parent="@android:style/Theme.AppCompat">
        <item name="android:windowActionBarOverlay">true</item>

        <!-- Support library compatibility -->
        <item name="windowActionBarOverlay">true</item>
    </style>
####当设置覆盖模式后，会使得部分应该显示为visible的布局在actionbar下面显示很模糊，为了确保这部分完全可视，我们可以给这部分布局的顶部设置margin或padding，属性值设置为actionBarSize,如下（android3.0及以上）
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:paddingTop="?android:attr/actionBarSize">
	    ...
	</RelativeLayout>
####当设置为android2.1及以上可用时，改为
	<!-- Support library compatibility -->
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:paddingTop="?attr/actionBarSize">
	    ...
	</RelativeLayout>


