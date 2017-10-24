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
```

* 根据包名查找已安装的 apk 路径

```markdown
adb shell pm path <packagename>
```

* adb 获取 root 权限 (机器须已 root )

```markdown
adb root
```

* 重新挂载分区 (通常在执行 adb root 后，让原本不可读写文件变得可读写前执行此命令)

```markdown
adb remount
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