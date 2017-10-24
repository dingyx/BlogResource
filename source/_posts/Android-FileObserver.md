---
layout: post
title: Andrid FileObserver 监听文件变化
date: 2017-09-07 19:30:51
tags: Android 
reward: false
---

FileObserver 是一个用来监听文件变化的抽象类，子类必须实现事件处理的方法 onEvent(int, String)  。由于最近做了两个项目都用上就查阅了官方文档，写下这篇文章记录一下。

> 每一个 FileObserver 实例用来监听一个文件或者文件夹，当文件夹被监听时，任意子文件或文件夹的访问或修改都会触发事件。**注意：**FileObserver 不能监听到二级子文件夹或文件的变化。

先附上官方的 FileObserver 链接：[https://developer.android.google.cn/reference/android/os/FileObserver.html](https://developer.android.google.cn/reference/android/os/FileObserver.html)

<!-- more -->
* 所有常量 (可监听的事件)

|   Constant    |         描述         |
| :-----------: | :----------------: |
|    ACCESS     |       文件被读取        |
|    ATTRIB     |   权限 所有者 时间戳被修改    |
| CLOSE_NOWRITE |   打开并关闭了文件夹(未修改)   |
|  CLOSE_WRITE  |   打开并关闭了文件夹(有修改)   |
|    CREATE     | 监控的文件夹下创建了子文件或文件夹  |
|    DELETE     | 监控的文件夹下删除了子文件或文件夹  |
|  DELETE_SELF  | 被监控的文件或文件夹被删除，监控停止 |
|    MODIFY     |       文件被修改        |
|  MOVED_FROM   |  被监控文件夹有子文件或文件夹移走  |
|   MOVED_TO    | 被监控文件夹有子文件或文件夹被移入  |
|   MOVE_SELF   |    被监控文件或文件夹被移动    |
|     OPEN      |     文件或文件夹被打开      |
|  ALL_EVENTS   |       以上所有事件       |


* 方法说明

  * Public constructors 构造方法

    * FileObserver(String path, int mask)

      > 为 path 路径的文件或者文件夹创建一个文件观察者。
      > path--文件路径 , mask--监听事件类型

    * FileObserver(String path)

      > 创建一个文件观察者
      > == FileObserver(path, FileObserver.ALL_EVENTS).)

  * Public methods 公共方法

    * onEvent(int event, String path)           -----void

      > 抽象方法，当监听到文件变化会回调此方法
      >
      > event 事件类型 , path 触发事件的文件相对监听文件的路径( 某些情况 path 可能为 null , 像监听的是一个文件，或者 MOVE_SELF 等事件)

    * startWatching( )                                       ----void

      > 开始监听

    * stopWatching( )                                       -----void

      > 结束监听



* 基本用法示例

  ```java
  // TestFileObserver 文件观察类
  class TestFileObserver extends FileObserver {

  	// path 为 需要监听的文件或文件夹
  	public TestFileObserver(String path) {
  		super(path,FileObserver.ALL_EVENTS);
  	}
  	
  	@Override
  	public void onEvent(int event, String path) {
  		// 如果文件修改了 打印出文件相对监听文件夹的位置
  		if(event==FileObserver.MODIFY){
  			Log.d("edong",path);
  		}
  	}
  }
  ```


  // 使用方法 1.初始化 TestFileObserver
  TestFileObserver testFileObserver = new TestFileObserver("/sdcard/Download/File");
  // 2.开始监听
  testFileObserver.startWatching();

> 上述代码只是一个简单用法的介绍，在我工作的项目中用到的情况是
> 智能家居设备是 Android 系统，在底层实现了一套唤醒机制，当设备唤醒时底层会将唤醒状态，唤醒角度等值写入一个文件。应用层使用 FileObserver 来监听这个文件，当文件变化时认为设备被唤醒，就去读取该文件获取唤醒相关的数值。
