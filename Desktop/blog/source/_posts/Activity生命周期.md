---
title: Activity生命周期
date: 2021-10-15 14:44:53
categories: Android
tags: Android
author: 小朱
comments: true
---

# Actvity的生命周期

## Activity的生命周期概念

为了在 Activity 生命周期的各个阶段之间导航转换，Activity 类提供六个核心回调：`onCreate()`、`onStart()`、`onResume()`、`onPause()`、`onStop()` 和 `onDestroy()`。当 Activity 进入新状态时，系统会调用其中每个回调。

<!-- more -->

![浮图秀图片_developer.android.com_20211015181043.png](https://i.loli.net/2021/10/15/kEZ8o9dVqeFbGaX.png)

如上图这就是一个Activity的生命周期的简化图示

- **onCreate ( )**: 表示Activity正在被创建。在这个方法中我们可以进行Activity的一些初始化工作，比如使用setContentView加载布局，初始化Activity所需要的数据等。

- **onStart ( )** : 表示Activity正在被启动，即将开始，此时Activity已经可见了，但是还没有出现在前台，还无法和用户进行交互。这个时候我们可以理解为Activity已经被显示出来了，但是我们还看不到。

- **onResume ( )** : 表示Activity不仅可见了，并且可以出现在前台开始活动。

- **onPause ( )** :表示Activity正在停止，正常情况下，紧接着onStop就会被调用。

- **onStop ( )** :表示Activity即将停止，此时Activity不可见。这个阶段可以做一些微重量级的回收工作，同样不能太耗时。

- **onDestroy ( )** : 表示Activity即将被销毁，也是Activity的最后一个回调。在这里，我们可以做一些回收工作和最终的资源释放。

- **onRestart( )** ：表示Activity正在重新启动。当当前的Activity从不可见的状态转变为可见的状态时候，onRestart就会被调用。这种情形一般是用户行为所导致的，比如当用户按Home键切换到桌面或者一个新的Activity，这是当前的Activity就会暂停（即onPause和onStop被执行了），接着用户又回到了这个Activity，这时就会调用onRestart方法了。

从整个生命周期来说，onCreate和onDestroy是配对的，分别标识着Activity的创建和销毁，并且只可能有一次调用。

从Activity是否在前台来说，onResume和onPause是配对的，随着用户的操作或设备屏幕的点亮和熄灭，这两个方法可以被多次调用。

从Activity是否可见来说，onStart和onStop是配对的，随着用户的操作或设备屏幕的点亮和熄灭，这两个方法可以被多次调用。

## Activity生命周期
