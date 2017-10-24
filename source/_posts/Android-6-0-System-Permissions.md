---
layout: post
title: Android 6.0 运行时权限说明与封装
date: 2017-01-16 22:53:51
tags: Android 
reward: false
---

### 运行时权限说明

Android 6.0引入了一种新的权限模式，将系统的权限分为正常权限和危险权限。开发者在使用到危险权限相关的功能时不仅要在manifest中配置，还要在代码中进行权限的判断和获取。

* 正常权限：不涉及用户的隐私，不需要在运行时向其授权。主要是震动，蓝牙，访问网络等权限。[查看所有正常权限](https://developer.android.google.cn/guide/topics/permissions/normal-permissions.html)
* 危险权限：涉及用户的隐私，需要在运行时向其授权。主要是使用相机，拨打电话，收发短信，获取位置信息，读写sd卡等权限。[此页面下拉查看所有危险权限及分组](https://developer.android.google.cn/guide/topics/permissions/requesting.html)
> 系统将危险权限进行了分组，当用户对组内的某一个权限进行了授权，下次请求组内其他权限时系统会默认直接授予。

<!-- more -->
### 相关API

* 检查权限
  当应用需要危险权限时，每次执行这一操作都必须检查是否具有该权限。检查是否具有某项权限调用 ContextCompact.checkSelfPermission() 方法。

```java
/**
*  @return 
*  已授权 PackageManager.PERMISSION_GRANTED
*  未授权 PERMISSION_DENIED。
*/
public static int checkSelfPermission(Context context,
    String permission) ｛
｝
```

* 判断用户是否拒绝过授予权限
  Android提供了 shouldShowRequestPermissionRationale() 方法,让APP在用户拒绝权限后，再次请求权限时，可以在合适的时候可以提示用户使用权限的原因。

```java
/**
*  @return 
*  true--用户上次请求权限时被拒绝
*  false-- 用户已经同意权限，拒绝权限并点击了不再提示
*/
public static boolean shouldShowRequestPermissionRationale(
                        Activity activity,String permission){
}
```

* 请求权限
  如果应用不具有某一权限，在调用该功能时必须先调用
   requestPermissions() 方法，来请求相应的权限。

```java
/**
* activity         当前activity
* permission       请求的权限
* requestCode      请求码
*/
public static void requestPermissions(Activity activity,
                                    String[] permissions,
                                    int requestCode) {
}
```

* 处理权限的回调

```java
@Override
/**
* requestCode     请求码
* permission      请求的权限
* grantResults    请求结果  
*/
public void onRequestPermissionsResult(int requestCode, 
                                String[] permissions, 
                                int[] grantResults) {
    
}
```

### 运行时权限的封装
首先大家可以 [点击这里](https://developer.android.google.cn/training/permissions/requesting.html) 查看官方文档给的请求权限写法。

为了简化对权限请求的操作，下面介绍一种对权限请求操作封装的方法。代码是看完郭霖的直播课 [Android 6.0运行时权限讲解](http://edu.csdn.net/course/detail/3539?locationNum=5&fps=1) 记录后写下来的。

1. 首先定义一个 Listener 接口，对权限请求操作回调。
```java
public interface PermissionListener {
    void onGranted();
    void onDenied(List<String> deniedPermission);
}
```

2. 因为权限请求操作时需要用到 Activity 作为参数，为了使权限请求可以在util类等其他非 Activity 类中使用，定义一个 ActivityCollector 来作为
   Activity 管理器以便在其他类中获取当前运行栈顶 Activity 。
```java
public class ActivityCollector {

    private static List<Activity> activityList = new ArrayList<>();

    public static void addActivity(Activity activity) {
        activityList.add(activity);
    }

    public static void removeActivity(Activity activity) {
        activityList.remove(activity);
    }
	// 获取栈顶 Activity
    public static Activity getTopActivity() {
        if (activityList.isEmpty())
            return null;
        return activityList.get(activityList.size() - 1);
    }
}
```

3. 在 Android 开发中都会用到 BaseActivity 来对公共内容进行整理封装，然后让其他 Activity 都继承 BaseActivity，接下来我们将权限请求操作封装在 BaseActivity 中。为了向下兼容，我们让 BaseActivity 继承 v7 包中的
   AppCompatActivity 。 
```java
public class BaseActivity extends AppCompatActivity {

    public static PermissionListener mListener;
    public static final int REQUEST_CODE = 1;

	// onCreate/onDestory 中保存 Activity
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityCollector.addActivity(this);
    }
    @Override
    protected void onDestroy() {
        super.onDestroy();
        ActivityCollector.removeActivity(this);
    }
	
    public static void requestRuntimePermission(String[] permissions, 
                                      PermissionListener listener) {
		// 获取栈顶 Activity
        Activity topActivity = ActivityCollector.getTopActivity();
        if (topActivity == null)
            return;
        mListener = listener;
        // 需要请求的权限列表
        List<String> requestPermisssionList = new ArrayList<>();
		// 检查权限  是否已被授权
        for (String permission : permissions) {
            if (ActivityCompat.checkSelfPermission(topActivity, permission) 
                      != PackageManager.PERMISSION_GRANTED)
                // 未授权时添加该权限
                requestPermisssionList.add(permission);
        }

        if (requestPermisssionList.isEmpty())
            // 所有权限已经被授权过 回调 Listener onGranted 方法 已授权
            listener.onGranted();
        else
			// 进行请求权限操作
            ActivityCompat.requestPermissions(topActivity,  
					requestPermisssionList.toArray(new String[requestPermisssionList.size()]), 
					REQUEST_CODE);

    }

	// 请求权限的回调
    @Override
    public void onRequestPermissionsResult(int requestCode, 
			@NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);

        switch (requestCode) {
            case REQUEST_CODE: {
				
                List<String> deniedPermissionList = new ArrayList<>();
				// 检查返回授权结果不为空
                if (grantResults.length > 0) {
                    // 判断授权结果
                    for (int i = 0; i < grantResults.length; i++) {
                        int result = grantResults[i];
                        if (result != PackageManager.PERMISSION_GRANTED)
							// 保存被用户拒绝的权限
                            deniedPermissionList.add(permissions[i]);
                    }
                    if (deniedPermissionList.isEmpty())
						// 都被授权  回调 Listener onGranted 方法 已授权
                        mListener.onGranted();
                    else
						// 有权限被拒绝 回调 Listner onDeynied 方法
                        mListener.onDenied(deniedPermissionList);
                }
                break;
            }
            default:
                break;
        }
    }
}
```

4. 封装完成后，使用危险权限相关操作时在 Activity 中只需要重写requestRuntimePermission() 就可以，其他类中则只需要调用BaseActivity.requestRuntimePermission() 方法。
```java
BaseActivity.requestRuntimePermission(
				new String[]{Manifest.permission.ACCESS_COARSE_LOCATION}, 
				new PermissionListener() {

      @Override
      public void onGranted() {
        // 已被授权 可执行相应权限操作
      }

      @Override
      public void onDenied(List<String> deniedPermission) {
          // 权限被拒绝

      }
});
```