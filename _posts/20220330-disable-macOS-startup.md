---
title: 关闭 macOS 中顽固的自启动应用
date: 2022-03-30 13:26:37
description: 对于无法在系统偏好设置->用户/群组界面中取消随系统启动的应用，可以通过本文的办法取消随系统启动。
---

> 操作系统版本 macOS Monterey 12.2.1

## 简单的关闭「登录项」的方法

macOS 取消随系统启动应用，可以在「系统偏好设置」下 「用户与群组」窗口中，选择「登录项」选项卡，删除或者取消随系统启动的应用。

但对于个别应用，例如 Lantern ，这个办法并没有效果。因为无法在登录项中看到对应的应用，就无法取消。

## 解决顽固启动项的方法

使用应用的关键词，在以下几个文件夹下寻找是否有对应的文件。

```sh
# ~/Library/Preferences （当前用户设置的进程）
# ~/Library/LaunchAgents （当前用户的守护进程）
# /Library/LaunchAgents/ （管理员设置的用户进程）
# /Library/LaunchDaemons/ （管理员提供的系统守护进程）
# /System/Library/LaunchAgents/ （Mac操作系统提供的用户进程）
# /System/Library/LaunchDaemons/ （Mac操作系统提供的系统守护进程）
$ sudo find ~/Library/Preferences ~/Library/LaunchAgents /Library/LaunchAgents/ /Library/LaunchDaemons/ /System/Library/LaunchAgents/ /System/Library/LaunchDaemons/ -name "*lantern*"
/Users/shiqiang/Library/LaunchAgents/org.getlantern.plist
```

打开找到的文件，将对应的 true 改为 false 。

```xml
<?xml version="1.0" encoding="UTF-8"?>                                                                                                                                       
    <!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN"
        "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>Label</key>
        <string>org.getlantern</string>
        <key>ProgramArguments</key>
        <array>
        <string>/Applications/Lantern.app/Contents/MacOS/lantern</string>
        <string>-startup</string>
        </array>
        <key>RunAtLoad</key>
        <true/>
    </dict>
    </plist>
```

修改后如下

```xml
<?xml version="1.0" encoding="UTF-8"?>                                                                                                                                       
    <!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN"
        "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
        <key>Label</key>
        <string>org.getlantern</string>
        <key>ProgramArguments</key>
        <array>
        <string>/Applications/Lantern.app/Contents/MacOS/lantern</string>
        <string>-startup</string>
        </array>
        <key>RunAtLoad</key>
        <false/>
    </dict>
    </plist>
```

重启后，可以验证 Lantern 不再随系统启动了。
