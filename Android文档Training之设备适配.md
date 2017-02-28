#语言适配
在android开发过程我们习惯或者说应该习惯把一些资源文件单独拿出来放到资源文件夹中那个，比如说string，在不同地区显示不同的语言，比如说我们把手机上的语言切换成西班牙语，这时我们的app就应该也切换到默认的西班牙语文件资源，这样可以使得我们的app更加国际化。

我们知道，在android studio中新建一个工程的时候res文件夹下会有value文件夹，这个文件夹一般存放string style integer color dimen等资源文件，默认的value是以英语为首选语言的，例如
###English (默认地点), /values/strings.xml:
	<?xml version="1.0" encoding="utf-8"?>
	<resources>
	    <string name="title">My Application</string>
	    <string name="hello_world">Hello World!</string>
	</resources>
###Spanish（西班牙）, /values-es/strings.xml:
	<?xml version="1.0" encoding="utf-8"?>
	<resources>
	    <string name="title">Mi Aplicación</string>
	    <string name="hello_world">Hola Mundo!</string>
	</resources>
###French（法国）, /values-fr/strings.xml:
	<?xml version="1.0" encoding="utf-8"?>
	<resources>
	    <string name="title">Mon Application</string>
	    <string name="hello_world">Bonjour le monde !</string>
	</resources>
####注意到它们的区别在于文件夹名称不同和对应的语言分别为英语、西班牙语和法语.

##资源文件的使用
###java代码中的使用示例
	// Get a string resource from your app's Resources
	String hello = getResources().getString(R.string.hello_world);
	
	// Or supply a string resource to a method that requires a string
	TextView textView = new TextView(this);
	textView.setText(R.string.hello_world);
###xml中使用示例
	<TextView
	    android:layout_width="wrap_content"
	    android:layout_height="wrap_content"
	    android:text="@string/hello_world" />
#屏幕适配
android各种各样的设备都有两个属性 size和density，我们都希望自己做的app能运行在各种各样的手机上适配各种各样的size和density.
###android手机有四种size: small, normal, large, xlarge
###android手机有四种density: low (ldpi), medium (mdpi), high (hdpi), extra high (xhdpi)
考虑到不同android设备的不同属性，我们也是把不同的资源文件放到不同的资源文件夹中，这点和语言适配是基本类似的
##创建不同的布局
####创建不同的歌布局文件夹res/layout{-size}/. 其中size可以是small, normal, large, xlarge，考虑到屏幕方向（oritation）也是一种新的屏幕尺寸（宽高相反 portrait/landscape），于是我们又有了文件夹res/layout{-size}-land/.实际文件夹如下
	MyProject/
	    res/
	        layout/              # default (portrait)
	            main.xml
	        layout-land/         # landscape
	            main.xml
	        layout-large/        # large (portrait)
	            main.xml
	        layout-large-land/   # large landscape
	            main.xml
##创建不同的bitmap
####不同的density对应的名称
    xhdpi: 2.0
    hdpi: 1.5
    mdpi: 1.0 (baseline)
    ldpi: 0.75
####这意味着如果你为xhdpi设备做了一张200*200的图片，你也得为hdpi做一张150*150的图片，为mdpi做一张100*100的图片，为ldpi做一张75*75的图片，这样drawable资源文件夹示意图如下：
	MyProject/
	    res/
	        drawable-xhdpi/
	            awesomeimage.png
	        drawable-hdpi/
	            awesomeimage.png
	        drawable-mdpi/
	            awesomeimage.png
	        drawable-ldpi/
	            awesomeimage.png
#Android版本适配
####新版本的android系统一般都有新的比较好的特性，这时你还需要兼容低版本的系统直到它们升级到最新系统.为了获得最好的用户体验，你必须使用support library.它使得旧版本的系统可以使用最版本的特性.
####在做app时，需要具体设置最小支持sdk版本及目标sdk版本（app->build.gradle）
	android {
	    compileSdkVersion 25
	    buildToolsVersion "25.0.1"
	
	    defaultConfig {
	        applicationId "huang.xiang.myapplication"
	        minSdkVersion 15
	        targetSdkVersion 25
	        versionCode 1
	        versionName "1.0"
	    }
	    buildTypes {
	        release {
	            minifyEnabled false
	            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
	        }
	    }
	}
####我们可以在运行时检查当前系统的sdk版本，示例如下
	private void setUpActionBar() {
	    // Make sure we're running on Honeycomb or higher to use ActionBar APIs
	    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
	        ActionBar actionBar = getActionBar();
	        actionBar.setDisplayHomeAsUpEnabled(true);
	    }
	}
####注意：在android开发中对xml文件的解析是安全的，当某一属性只在高版本的系统支持时，在低版本系统中解析遇到此属性将会被直接忽视，不会造成app的crash，所以我们可以随意的在xml中使用新版本的属性。
###使用系统的style样式和Theme主题
使你的activity看起来像一个对话框:

	<activity android:theme="@android:style/Theme.Dialog">

使你的activity有一个透明的背景:

	<activity android:theme="@android:style/Theme.Translucent">

使用 /res/values/styles.xml中的自定义主题:

	<activity android:theme="@style/CustomTheme">
把主题加到app中所有activity上，增加android:theme属性给Manifest中的application节点

	<application android:theme="@style/CustomTheme">
