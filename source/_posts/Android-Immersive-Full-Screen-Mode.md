---
layout: post
title: Android 沉浸式模式与常见状态栏和导航栏
date: 2016-11-03 11:56:06
tags: Android
reward: false
---

### Android沉浸式模式
官方称沉浸式状态栏为沉浸式模式。

* 什么是沉浸式？
  **沉浸式就是让人专注当前的(由设计者营造)情境下感到愉悦和满足，而忘记真实的情境。**    

* 什么是Android中的沉浸式？
  **当启用该模式，应用程序的界面将占据整个屏幕，自动隐藏系统的状态栏和导航栏，让应用程序内容可以在最大显示范围呈现，增加大屏体验，而当需要查看通知的时候只需要从顶部向下滑动就能呼出通知栏。**

<!-- more -->
* 一个应用程序界面有有如下元素：
  ![](http://upload-images.jianshu.io/upload_images/3512867-13ce9110b7235f7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 而打造沉浸式模式的用户体验就是要将这些系统元素全部隐藏，只留下主题内容部分。先看一下效果图：
  ![](http://upload-images.jianshu.io/upload_images/3512867-94818973391eec0d.gif?imageMogr2/auto-orient/strip)

进入程序时，自动隐藏了状态栏和导航栏。当用户从顶部向下滑动就能呼出半透明的状态栏和导航栏。

具体代码如下:
```java
public class MainActivity extends AppCompatActivity {
    
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
    
    public void onWindowFocusChanged(boolean hasFocus) {
        super.onWindowFocusChanged(hasFocus);

        View mDecorView = getWindow().getDecorView();

        mDecorView.setSystemUiVisibility(
                View.SYSTEM_UI_FLAG_LAYOUT_STABLE
                        | View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
                        | View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
                        | View.SYSTEM_UI_FLAG_HIDE_NAVIGATION // hide nav bar
                        | View.SYSTEM_UI_FLAG_FULLSCREEN // hide status bar
                        | View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
        );
    }

}
```
*   上面使用的 SYSTEM_UI_FLAG_IMMERSIVE_STICKY 官方称为粘性沉浸式模式。 View.SYSTEM_UI_FLAG_IMMERSIVE  为非粘性沉浸式，非粘性沉浸式状态栏和导航栏显示后不再自动隐藏。

> 注意：onWindowFocusChanged 方法里面的代码不能直接写在 onCreate 方法中，如果这么写当应用处于后台 Stoped 状态，下次启动应用便执行不到处理沉浸式的代码。Activity 生命周期中，真正的 visible 时间点是 onWindowFocusChanged() 函数被执行时，所以我们将这段代码写在这个方法中。

可以参考官方文档：[https://developer.android.google.cn](https://developer.android.google.cn/training/system-ui/immersive.html)
中文翻译版：[http://hukai.me/android-training-course-in-chinese](http://hukai.me/android-training-course-in-chinese/ui/system-ui/immersive.html)

### 其他常见状态栏和导航栏效果

* 隐藏 ActionBar 
```java
ActionBar actionBar = getSupportActionBar();
actionBar.hide();
```
上面的代码需要在放在 setContentView 方法之前，否则会出现 ActionBar 显示后再隐藏。如果整个应用没有用到 ActionBar 我们可以直接在 AndroidManifest.xml 中将 application 的 theme 设置为 NoActionBar ，如果只是某个 activity 不用也可以将这个 activity 的 theme 设置为 NoActionBar 。
```
<activity android:name=".MainActivity"
     android:theme="@style/Theme.AppCompat.Light.NoActionBar">
     <intent-filter>
           <action android:name="android.intent.action.MAIN"/>
         <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>
</activity>
```

效果图如下：

![](http://upload-images.jianshu.io/upload_images/3512867-777cc5d17ae4f3bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* 透明状态栏和导航栏

```java
//透明状态栏和导航栏，隐藏 ActionBar
getSupportActionBar().hide();

getWindow().getDecorView().setSystemUiVisibility(
	View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN|View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION);

getWindow().setNavigationBarColor(Color.TRANSPARENT);
getWindow().setStatusBarColor(Color.TRANSPARENT);
```

效果图如下：

![](http://upload-images.jianshu.io/upload_images/3512867-787824eceb48a743.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)