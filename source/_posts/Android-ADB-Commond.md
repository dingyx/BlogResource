---
layout: post
title: Android ADB 命令记录
reward: false
date: 2017-09-29 22:31:30
tags: Android
---

**记录平时用到的 adb 命令**

* 重启机器

```markdown
adb reboot
```

* 安装 apk

```markdown
adb install <ApkFilePath>
// eg. adb install F:\app\test.apk
// 可选参数 -r 允许覆盖安装  eg. adb install -r F:\app\test.apk
// -t 允许安装测试包        -s 安装到sd卡   
// -d 允许安装低版本包装包  -g 授权所有运行时权限    
```

* 卸载 apk

```markdown
adb uninstall <packagename>
```

<!-- more -->
* 启动 activity

```markdown
adb shell am start -n <packagename/.activityname>
// eg. adb shell am start -n com.edong.speaker/.MainActivity
```

* 启动 service

```markdown
adb shell am startservice -n <packagename/.servicename>
// eg. adb shell am startservice -n com.edong.speaker/.service.SpeakerService
```

* 强制停止应用

```markdown
adb shell am force-stop <packagename>
//eg. adb shell am force-stop com.edong.test
```

* 发送广播

```markdown
adb shell am broadcast [options] <INTENT>
// 向所有组件发广播 adb shell am broadcast -a android.intent.action.BOOT_COMPLETED
// 只向某个广播 BroadcastReceiver 发广播 adb shell am broadcast -a android.intent.action.BOOT_COMPLETED -n com.edong.test/.BootCompletedReceiver
```

* 模拟按键输入

```markdown
adb shell input keyevent <keycode> 
// HOME键 eg. adb shell input keyevent 3
```

* 从 Android 设备拉取文件到电脑

```markdown
adb pull <AndroidFilePath> <PcDirPath>
// eg. adb pull /sdcard/configmanager.json F:\app
```

* 从电脑 copy 文件到 Android 设备

```markdown
adb push <PcFilePath> <AndroidDirPath>
// eg. adb push F:\app\configmanager.json /sdcard/
```

* 输出 Android 设备 logcat  日志到电脑

```markdown
adb logcat > <PcFilePath>
// eg. adb logcat > D:/log.txt
```

* 进入 Shell 模式  (进入 Shell 后即可使用很多 Linux 下的 Shell 命令)

```markdown
adb shell
```

* 删除设备上某个文件

```markdown
adb shell rm -rf <AndroidFilePath>
//进入 Shell 删除文件可以直接 rm -rf <AndroidFilePath>
```

* 列出所有已安装 apk 包

```markdown
adb shell pm list packages
// 列出系统应用 adb shell pm list packages -s
// 列出第三方应用 adb shell pm list packages -3
// 列出包名含有"test"的应用 adb shell pm list packages test
```

* 查看前台 Activity

```markdown
adb shell dumpsys activity activities | grep mFocusedActivity
```

* 查看正在运行的 Services

```markdown
adb shell dumpsys activity services [<packagename>]
// 指定 packagename 则列出对应包名的 Services
// 不指定 则列出所有 Services
```

* 查看 WIFI 密码 (需要root权限)

```markdown
adb shell
su
cat /data/misc/wifi/*.conf
```

* 查看正在运行的所有进程

```markdown
adb shell ps
```

* 根据进程名字查看进程 pid

```markdown
adb shell pidof <ProcessName>
```

* 杀掉某个进程

```markdown
adb shell killall <ProcessName>
adb shell kill <ProcessPid>
```

* 根据包名查找已安装的 apk 路径

```markdown
adb shell pm path <packagename>
```

* 查看 Mac 地址

```
adb shell cat /sys/class/net/wlan0/address
```

* 内存信息

```markdown
adb shell cat /proc/cpuinfo
```

* 查看资源占用情况

```markdown
adb shell top
```

* adb 获取 root 权限 (机器须已 root )

```markdown
adb root
```

* 重新挂载分区 (通常在执行 adb root 后，让原本不可读写文件变得可读写前执行此命令)

```markdown
adb remount
```


* 屏幕截图

```markdown
adb shell screencap -p /sdcard/screen.png
```

* 录制屏幕

```markdown
adb shell screenrecord /sdcard/filename.mp4
```

