---
layout: post
title: Andrid 多语言适配
date: 2016-11-02 23:51:51
tags: Android 
brief: "突然想起以前刚工作遇到的中英文适配问题"
reward: false
---

### 应用程序根据系统设置语言实现多语言适配

应用程序默认只要配置不同语言的文件，在启动应用时都会根据系统语言而加载对应的语言资源文件。例如支付宝，微信，微博等都实现了中英文的适配。下面的例子我们将实现中文的适配。

* 创建相应语言的文件    
  右键 res 文件夹 File-->New-->Android resource file 选择 Locale 看到如下界面：
  我们选择新增的语言为中文，将文件名设为 strings 
  <!-- more -->
  ![](http://upload-images.jianshu.io/upload_images/3512867-c87f25958de5bc9c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  点击确定后可以看到生成如下目录及文件（新增了 values-zh 文件夹和 strings.xml 文件）：
  ![](http://upload-images.jianshu.io/upload_images/3512867-8163672f1bbc2c88?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* values\strings.xml 中代码如下：
```
<resources>
    <string name="app_name">LanguagesTest</string>
    <string name="hello_world">Hello World!</string>
</resources>
```
* 修改 values-zh\strings.xml 中代码如下：
```
<resources>
    <string name="app_name">语言测试</string>
    <string name="hello_world">你好，世界！</string>
</resources>
```

* 此时运行项目，当系统语言设置为中文时显示如下：
  ![](http://upload-images.jianshu.io/upload_images/3512867-b0ea965857980068?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 系统语言设置为中文之外的其他语言显示如下：
  ![](http://upload-images.jianshu.io/upload_images/3512867-1db7c0bdebdca07f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注意：当应用程序没有退出，进入系统修改了语言设置。再次进入时应用会重新载入。类似于横竖屏切换时的应用生命周期。

### 应用内实现语言设置

* 实现效果
  应用内有一个设置按钮，点击设置时弹出 dialog 提示语言选择。用户选择完成后，重启应用并按用户设置加载对应的语言。

点击设置按钮出现如下界面：
![](http://upload-images.jianshu.io/upload_images/3512867-dabb40c4532c85ec?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 应用内实现语言的设置需要开发者在代码中实现，而不跟随系统语言变化而改变。像微信在程序内就有语言设置，下面代码实现的效果和微信类似。语言设置完成后重启应用，重新加载不同的语言。同时，我们将设置的语言类型存储起来，下次启动应用先读取设置的语言，而不跟随系统的设置语言，然后再加载应用。

* 具体实现代码如下：

```java
public class MainActivity extends AppCompatActivity {

    ImageButton btn_settings;
    AlertDialog dialog;

    protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);
        //读取 SharedPreferences 数据，初始化语言设置
        setLanguage();

        getSupportActionBar().hide();
        setContentView(R.layout.activity_main);

        btn_settings = (ImageButton) findViewById(R.id.btn_settings);

        //点击设置按钮进入语言设置
        btn_settings.setOnClickListener(new View.OnClickListener() {

            public void onClick(View view) {

                //创建单选框
                final AlertDialog.Builder builder = new
                        AlertDialog.Builder(MainActivity.this);
                builder.setSingleChoiceItems(new String[]{"Auto", "简体中文"},
                        getSharedPreferences("language", Context.MODE_PRIVATE).getInt("language",0),
                        new DialogInterface.OnClickListener() {
                            //点击单选框某一项以后
                            public void onClick(DialogInterface dialogInterface, int i) {

                                //将选中项存入 SharedPreferences ，以便重启应用后读取设置
                                SharedPreferences preferences = getSharedPreferences("language", Context.MODE_PRIVATE);
                                SharedPreferences.Editor editor = preferences.edit();
                                editor.putInt("language",i);
                                editor.apply();
                                dialog.dismiss();
                               
                                Intent intent = new Intent(MainActivity.this, MainActivity.class);
                                intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
                                startActivity(intent);

                                /* 重新在新的任务栈开启新应用
                                Intent intent = new Intent(MainActivity.this, MainActivity.class);
                                intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TASK);
                                startActivity(intent);
                                android.os.Process.killProcess(android.os.Process.myPid()); */
                            }
                        });

                dialog = builder.create();
                dialog.show();
            }
        });

    }


    private void setLanguage() {

        //读取 SharedPreferences 数据，默认选中第一项
        SharedPreferences preferences = getSharedPreferences("language", Context.MODE_PRIVATE);
        int language = preferences.getInt("language", 0);

        //根据读取到的数据，进行设置
        Resources resources = getResources();
        DisplayMetrics displayMetrics = resources.getDisplayMetrics();
        Configuration configuration = resources.getConfiguration();

        switch (language){
            case 0:
                configuration.setLocale(Locale.getDefault());
                break;
            case 1:
                configuration.setLocale( Locale.CHINESE);
                break;
            default:
                break;
        }

        resources.updateConfiguration(configuration,displayMetrics);

    }

}
```