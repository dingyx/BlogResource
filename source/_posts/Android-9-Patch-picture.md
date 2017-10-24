---
layout: post
title: Andrid 利用 9-Patch 图片制作聊天框
date: 2016-11-02 23:51:51
tags: Android 
reward: false
---

* 什么是9-Patch图片?
  **一种被特殊处理过的 png 图片，可以指定哪些区域能够拉伸，哪些区域不能拉伸，哪些区域可以显示内容。**

* 使用场景
  主要是用于对话框等拉伸能够不变形的场景

* 创建 .9.png 图片
  选中一张 png 图片，右键 Create 9-Patch file 
  <!-- more -->
  ![](http://upload-images.jianshu.io/upload_images/3512867-323ac8998352b4a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 注意：9-Patch 图片要放在 drawable 文件夹下，否则编译时会报错。后缀名必须是 .9.png 。

* 编辑 .9.png 图片
  打开 .9.png 图片，选择 9-Patch 即可对图片进行编辑。
  ** 编辑图片时上下左右四个方向的黑线，用来控制图片可拉伸和可显示区域。上和左控制图片可拉伸区域，下和右控制图片可显示区域。**
  ![](http://upload-images.jianshu.io/upload_images/3512867-0c82d5bf86c8f724.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 使用 9.pn g图片
  在布局文件中，将 TextView 的背景设置为制作好的 9-Patch 图片。
```
<TextView
    android:background="@drawable/chat"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="20sp"
    android:paddingTop="12dp"
    android:text="@string/chat_content"/>
```
当 TextView 中内容变化时，显示效果如下:
![](http://upload-images.jianshu.io/upload_images/3512867-f908bd1a9e7143cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/3512867-bb739c01988b6f06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/3512867-66fa92f2555eabe5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)