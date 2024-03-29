---
title: Android简介
date: 2021-08-04 18:24:53
categories: Android
tags: Android
author: 小朱&&liu&&qiu
comments: true
stricky: 1
---

# 什么是Android？

## 简介

Android一词的本义指“[机器人](https://baike.baidu.com/item/机器人)”，同时也是[Google](https://baike.baidu.com/item/Google)于2007年11月5日宣布的基于[Linux](https://baike.baidu.com/item/Linux)平台的[开源](https://baike.baidu.com/item/开源)手机操作系统的名称，该平台由[操作系统](https://baike.baidu.com/item/操作系统)、中间件、[用户界面](https://baike.baidu.com/item/用户界面)和[应用软件](https://baike.baidu.com/item/应用软件)组成。

主要使用于[移动设备](https://baike.baidu.com/item/移动设备/9157757)，如[智能手机](https://baike.baidu.com/item/智能手机/94396)和[平板电脑](https://baike.baidu.com/item/平板电脑/1348389)，由美国[Google](https://baike.baidu.com/item/Google)公司和[开放手机联盟](https://baike.baidu.com/item/开放手机联盟/9064338)领导及开发。Android操作系统最初由[Andy Rubin](https://baike.baidu.com/item/Andy Rubin)开发，主要支持[手机](https://baike.baidu.com/item/手机/6342)。2005年8月由Google收购注资。2007年11月，Google与84家硬件制造商、软件开发商及电信营运商组建开放手机联盟共同研发改良Android系统。随后Google以Apache开源许可证的授权方式，发布了Android的源代码。第一部Android智能手机发布于2008年10月。Android逐渐扩展到平板电脑及其他领域上，如[电视](https://baike.baidu.com/item/电视/228945)、[数码相机](https://baike.baidu.com/item/数码相机/277472)、[游戏机](https://baike.baidu.com/item/游戏机/315328)、[智能手表](https://baike.baidu.com/item/智能手表/71070)等。

<!-- more -->

## Logo

Android的logo是由**Ascender**公司设计的，诞生于2010年，其设计灵感来源于男女上厕所门上的图形符号，于是布洛克绘制了一个简单的机器人，它的躯干就像锡罐的形状，头上还有两根天线，Android小机器人便诞生了。

<img src="https://i.loli.net/2021/08/03/cVCdhlNZrPT3OGe.png" style="zoom:50%;" />

## 体系架构

作为Android开发的程序员，我们也要了解Android的体系架构，我们的手机很大部分使用的Android系统，所以应该不难理解。Android的系统架构和其操作系统一样，采用了分层的架构。从架构图看，Android分为四个层，从高层到低层分别是应用程序层、应用程序框架层、系统运行库层和Linux[内核](https://baike.baidu.com/item/内核)层。

**应用程序**

Android会同一系列核心应用程序包一起发布，该应用程序包括客户端，SMS短消息程序，日历，地图，浏览器，联系人管理程序等。所有的应用程序都是使用JAVA语言编写的。

**应用程序框架**

开发人员也可以完全访问核心应用程序所使用的[API](https://baike.baidu.com/item/API)框架。该应用程序的架构设计简化了组件的重用;任何一个应用程序都可以发布它的功能块并且任何其它的应用程序都可以使用其所发布的功能块（不过得遵循框架的安全性）。同样，该应用程序重用机制也使用户可以方便的替换程序组件。即我们可以在开发我们自己的应用程序时，使用API框架用来简化我们的开发。

隐藏在每个应用后面的是一系列的服务和系统, 其中包括;

丰富而又可扩展的[视图](https://baike.baidu.com/item/视图)（Views），可以用来构建应用程序，即我们可以看到的手机应用界面的组成。 它包括列表（Lists），网格（Grids），文本框（Text boxes），按钮（Buttons）， 甚至可嵌入的web浏览器。

内容提供器（Content Providers）使得应用程序可以访问另一个应用程序的数据（如联系人数据库）， 或者共享它们自己的数据

资源管理器（Resource Manager）提供 非代码资源的访问，如本地字符串，图形，和布局文件（Layout files ）。

通知管理器 （Notification Manager）使得应用程序可以在状态栏中显示自定义的提示信息。

活动管理器（ Activity Manager）用来管理应用程序生命周期并提供常用的导航回退功能。就是我们在打开、暂停和关闭应用程序等时进行的管理操作。

**系统运行库**

Android 包含一些C/C++库，这些库能被[Android系统](https://baike.baidu.com/item/Android系统)中不同的[组件](https://baike.baidu.com/item/组件)使用。它们通过 Android 应用程序框架为[开发者](https://baike.baidu.com/item/开发者)提供服务。

**系统结构技术性太强就不做过多介绍了**



## 应用组件

Android开发四大组件分别是：活动（[Activity](https://baike.baidu.com/item/Activity)）： 用于表现功能。服务（[Service](https://baike.baidu.com/item/Service)）： 后台运行服务，不提供界面呈现。广播接收器（BroadcastReceiver）：用于接收广播。内容提供商（[Content Provider](https://baike.baidu.com/item/Content%20Provider)）： 支持在多个应用中存储和读取数据，相当于[数据库](https://baike.baidu.com/item/数据库)。

### 活动

Android 中，Activity是所有程序的根本，所有程序的流程都运行在Activity 之中，Activity可以算是开发者遇到的最频繁，也是Android 当中最基本的模块之一。在Android的程序当中，Activity 一般代表[手机](https://baike.baidu.com/item/手机)屏幕的一屏。如果把手机比作一个浏览器，那么Activity就相当于一个网页。在Activity 当中可以添加一些Button、Check box 等控件。可以看到Activity 概念和网页的概念相当类似。

一般一个Android 应用是由多个Activity 组成的。这多个Activity 之间可以进行相互跳转，例如，按下一个[Button](https://baike.baidu.com/item/Button)按钮后，可能会跳转到其他的Activity。和网页跳转稍微有些不一样的是，Activity 之间的跳转有可能返回值，例如，从Activity A 跳转到Activity B，那么当Activity B  运行结束的时候，有可能会给Activity A 一个返回值。这样做在很多时候是相当方便的。

当打开一个新的屏幕时，之前一个屏幕会被置为暂停状态，并且压入历史堆栈中。用户可以通过回退操作返回到以前打开过的屏幕。可以选择性的移除一些没有必要保留的屏幕，因为Android会把每个应用的开始到当前的每个屏幕保存在堆栈中。

### 服务

Service 是android 系统中的一种组件，它跟Activity 的级别差不多，但是他不能自己运行，只能后台运行，并且可以和其他组件进行交互。Service 是没有界面的长生命周期的代码。[Service](https://baike.baidu.com/item/Service)是一种程序，它可以运行很长时间，但是它却没有用户界面。这么说有点枯燥，来看个例子。打开一个音乐播放器的程序，这个时候若想上网了，那么，打开Android浏览器，这个时候虽然已经进入了浏览器这个程序，但是，歌曲播放并没有停止，而是在后台继续一首接着一首的播放。其实这个播放就是由播放音乐的Service进行控制。当然这个播放音乐的Service也可以停止，例如，当播放列表里边的歌曲都结束，或者用户按下了停止音乐播放的快捷键等。Service 可以在和多场合的应用中使用，比如播放多媒体的时候用户启动了其他Activity这个时候程序要在后台继续播放，比如在后台记录地理信息位置的改变等等，总之服务嘛，总是藏在后头的。

### 广播接收器

在Android 中，[Broadcast](https://baike.baidu.com/item/Broadcast)是一种广泛运用的在应用程序之间传输信息的机制。可以使用BroadcastReceiver  来让应用对一个外部的事件做出响应。这是非常有意思的，例如，当电话呼入这个外部事件到来的时候，可以利用BroadcastReceiver  进行处理。例如，当下载一个程序成功完成的时候，仍然可以利用BroadcastReceiver  进行处理。BroadcastReceiver不能生成UI，也就是说对于用户来说不是透明的，用户是看不到的。

### 内容提供

Content Provider 是Android提供的第三方应用数据的访问方案。

在Android中，对数据的保护是很严密的，一个应用所持有的数据库、文件等内容，都是不允许其他直接访问的。Android当然不会真的把每个应用都做成一座孤岛，它为所有应用都准备了一扇窗，这就是Content  Provider。

## 平台优势

### 开放性

在优势方面，Android平台首先就是其开放性，开发的平台允许任何移动终端厂商加入到Android联盟中来。显著的开放性可以使其拥有更多的开发者，随着用户和应用的日益丰富，一个崭新的平台也将很快走向成熟。

开放性对于Android的发展而言，有利于积累人气，这里的人气包括消费者和厂商，而对于消费者来讲，最大的受益正是丰富的软件资源。开放的平台也会带来更大竞争，如此一来，消费者将可以用更低的价位购得心仪的手机。同时也可以通过一些第三方优化过的系统通过刷机来实现更好的用户体验，如MIUI，Flyme等。

### 丰富的硬件

这一点还是与Android平台的开放性相关，由于Android的开放性，众多的厂商会推出千奇百怪，功能特色各具的多种产品。功能上的差异和特色，却不会影响到数据同步、甚至软件的兼容。

### 方便开发

Android平台提供给第三方开发商一个十分宽泛、自由的环境，不会受到各种条条框框的阻扰，可想而知，会有多少新颖别致的软件会诞生。

### Google应用

在互联网的[Google](https://baike.baidu.com/item/Google/86964)已经走过10年度历史，从搜索巨人到全面的互联网渗透，Google服务如地图、邮件、搜索等已经成为连接用户和互联网的重要纽带，而Android平台手机将无缝结合这些优秀的Google服务。



# 什么是木犀安卓组？

木犀 Android 组主要使用 Java、Kotlin 等语言开发基于 Android 系统上的 App。

## 开发语言

### java

Java是一门[面向对象](https://baike.baidu.com/item/面向对象)编程语言，不仅吸收了[C++](https://baike.baidu.com/item/C%2B%2B)语言的各种优点，还摒弃了C++里难以理解的[多继承](https://baike.baidu.com/item/多继承)、[指针](https://baike.baidu.com/item/指针/2878304)等概念，因此Java语言具有功能强大和简单易用两个特征。Java语言作为静态面向对象编程语言的代表，极好地实现了面向对象理论，允许程序员以优雅的思维方式进行复杂的编程。

### kotlin

Kotlin  [1] （科特林）是一个用于现代多平台应用的静态[编程语言](https://baike.baidu.com/item/编程语言/9845131) ，由 [JetBrains](https://baike.baidu.com/item/JetBrains) 开发。

Kotlin可以编译成[Java字节码](https://baike.baidu.com/item/Java字节码/13025120)，也可以编译成[JavaScript](https://baike.baidu.com/item/JavaScript/321142)，方便在没有[JVM](https://baike.baidu.com/item/JVM/2902369)的设备上运行。除此之外Kotlin还可以编译成[二进制代码](https://baike.baidu.com/item/二进制代码/4879654)直接运行在机器上（例如[嵌入式设备](https://baike.baidu.com/item/嵌入式设备/10055189)或[ iOS](https://baike.baidu.com/item/ iOS/45705)）。

Kotlin已正式成为[Android](https://baike.baidu.com/item/Android/60243)官方支持开发语言。

设计目标

1. 创建一种兼容[Java](https://baike.baidu.com/item/Java/85979)的语言

2. 让它比Java更安全，能够静态检测常见的陷阱。如：引用空[指针](https://baike.baidu.com/item/指针/2878304)

3. 让它比Java更简洁。


## 关于学习

每周都会有详细的任务内容，同时会有学长或学姐作为引导人。在大一我们会学习C语言基础和java语言，在打好基础后学习android开发。在大一上结束的寒假，我们会有一次miniproject活动，进行实际动手编写一个app。



# Java环境搭建

## 什么是JDK及其作用

​        JDK(Java Development Kit) 是 Java 语言的软件开发工具包([SDK](https://baike.baidu.com/item/SDK))。SE(JavaSE)，standard edition（标准版），是我们通常用的一个版本，从JDK 5.0开始，改名为Java SE。JDK是 [Java](https://baike.baidu.com/item/Java/85979) 语言的[软件开发工具包](https://baike.baidu.com/item/软件开发工具包/10418833)，主要用于[移动设备](https://baike.baidu.com/item/移动设备/9157757)、[嵌入式设备](https://baike.baidu.com/item/嵌入式设备/10055189)上的java应用程序。JDK是整个java开发的核心，它包含了JAVA的运行环境（JVM+Java系统类库）和JAVA工具。

​        总而言之，就是我们学习和使用Java的必备装备。工欲善其事，必先利其器。接下来就来带领大家走入Java世界的大门，期待以后与君同行。

##  JDK的下载与配置

### JDK的下载

​        上面已经大致介绍了Android的基本情况，了解了我们使用的开发语言，接下来会拉近咱们与编程语言的距离，带领大家配置Java的环境。

​        首先是要进行JDK的安装：这里是[下载链接](https://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html) https://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html

点进来，会看到如下页面

![](https://z3.ax1x.com/2021/07/28/W7mzy4.png)

**那么我们点击这个地方**

![](https://z3.ax1x.com/2021/07/28/W7KOYt.jpg)

**这里会跳转到如下页面↓**

![](https://z3.ax1x.com/2021/07/28/W74cB6.png)

我们向下翻会看到各种系统的安装的包和不同方法，Installer这里是下载后可直接安装的，另一种是需要解压等一系列操作，因为我的电脑是win10的系统，所以直接选择这个进行下载。

![](https://z3.ax1x.com/2021/07/28/W75VC4.png)

点击后会出现这个弹窗，这里这个勾选同意，后续步骤就和正常的安装电脑程序一样了！（这里要记一下安装的路径，后续步骤需要使用）

![](https://z3.ax1x.com/2021/07/28/W74vCQ.png)

**安装好了，我们离成为一只Java攻城狮就更进一步了**

![](https://www.runoob.com/wp-content/uploads/2018/08/1534411157-4157-3932295-b821f0e17887bcdf.png)

### 环境变量的配置

接下来要做的事是，配置环境变量，这一步就是让电脑激活你所安装的Java环境，使电脑真正的拥有Java环境。那咱们继续，如图找到“**我的电脑（此电脑）**”，打开

![](https://z3.ax1x.com/2021/07/28/W75Ls1.png)

在空白处右键点击，选择“**属性**”。

![](https://z3.ax1x.com/2021/07/28/W75cxs.png)

进入到如下页面，选择“**高级系统设置**”

![](https://z3.ax1x.com/2021/07/28/W7IpJe.png)

然后点击“**高级**”。

![](https://z3.ax1x.com/2021/07/28/W7IKzj.png)

这里我们看到环境变量（希望的曙光）

<center class="half">
    <img src="https://z3.ax1x.com/2021/07/28/W7IHk8.png" width="300"/>
    <img src="https://z3.ax1x.com/2021/07/28/W7Iqfg.png" width="300"/>
</center>
点击“**环境变量**”，会看到如下页面

![](https://z3.ax1x.com/2021/07/28/W7I8e0.png)

我们选择下面的“**系统变量**”，点击“**新建**”

![](https://z3.ax1x.com/2021/07/28/W7I0yR.png)

然后在弹窗输入

```
变量名：JAVA_HOME
变量值：电脑上JDK安装的绝对路径
```

例如我Java安装在C:\Program Files\Java\jdk.1.8.0_121(该文件夹内下能看到如下的文件)

![](https://www.runoob.com/wp-content/uploads/2018/08/1534411157-4157-3932295-b821f0e17887bcdf.png)

所以绝对路径是C:\Program Files\Java\jdk.1.8.0_121

如图输入

![](https://www.runoob.com/wp-content/uploads/2018/08/1534411114-3840-3932295-73be617cffdac223.png)

接着找到“**环境变量**”里的“**Path**”然后点击"**编辑**"

![](https://z3.ax1x.com/2021/07/28/W7IBO1.png)

点击新建，输入如下两个路径

```
%JAVA_HOME%\bin
%JAVA_HOME%\jre\bin
```

![5baa1d13920ae8e2543fdfabf2b1e330.png](https://img-blog.csdnimg.cn/img_convert/5baa1d13920ae8e2543fdfabf2b1e330.png)

通过“**上移**”将这两条弄着最顶端（QwQ）

最后回到“**系统变量**”进行“**新建**”，输入

```
变量名：CLASSPATH
变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;
```

![e55f5524bb2c3385ca1768450efc3be0.png](https://img-blog.csdnimg.cn/img_convert/e55f5524bb2c3385ca1768450efc3be0.png)

好耶，终于完成了。接下来让我们来检验“**劳动成果**”，进行验证。

### 验证

使用快捷键win + R打开“**运行**”，输入cmd（大小写都写）

![](https://z3.ax1x.com/2021/07/28/W7Isw6.png)

进入如图的黑窗输入“**Java**”，“**回车**”

![](https://z3.ax1x.com/2021/07/28/W7IWSH.png)

之后会弹出一堆的文字，如图是最后一部分

![](https://z3.ax1x.com/2021/07/28/W7Ih6A.png)

到这里，java环境就能正式投入生产了！



# 案例介绍

我们使用的编译软件是“**Intellij IDEA**”和“**Android Studio**”

下面来分别介绍一些简单的示例

## Intellij IDEA

简单介绍一下这个编译软件的使用

首先创建一个新的文件夹，我这里是创建过了，所以不是初始界面

![](https://z3.ax1x.com/2021/08/03/fiXJUJ.png)

我们点击左上角的**File**选择**New**选择**Project**

然后会看到这样的界面

![](https://z3.ax1x.com/2021/08/03/fiXvrT.png)

这里处理完后直接点击“**Next**，下一个也直接**Next**

然后我们来到这一步

![](https://z3.ax1x.com/2021/08/03/fijmZD.png)

点击**Finish**完成创建

在全新的窗口中打开那个文件夹，会来到如开场一下布局页面

![](https://z3.ax1x.com/2021/08/03/fijqTe.png)

我们所有的代码会存在这个src文件夹下，我们右键点击文件夹，如下图操作

![](https://z3.ax1x.com/2021/08/03/fivCm8.png)

然后输入文件名称，这里我选择的是 “Hello”

![](https://z3.ax1x.com/2021/08/03/fivkkQ.png)

可以看到，完成创建后编译器会自动帮你添加一部分代码

![](https://z3.ax1x.com/2021/08/03/fivelq.png)

那按照如下输入，一起打开开发世界的大门吧！

![](https://z3.ax1x.com/2021/08/03/fivLuV.png)

接着点击旁边的绿色运行图标，然后你将看到

![](https://z3.ax1x.com/2021/08/03/fix1Df.png)



~***后话***~

这里有比较简单的两道题，大家可以尝试一下，在下次直播时，会有巨巨巨强的学长学姐来给大家讲解。

1.使用Interllj IDEA在终端输出“Hello World”

2.尝试从键盘输入两个数，计算这两个数的和，并在终端输入

提示：int number1 = 1;这句的意思是定义一个int型的变量（整数型）number1，并赋值为1；

​             Scanner next = new Scanner(System.in); 这一句是创建一个Scanner类的实列next，作用是读取键盘输入的数字。用法：

​             int number1 = next.nextInt();就是定义一个int型的变量（整数型）number1，并赋值为键盘刚刚输入的数；

## Android Studio

和Intellij IDEA相类似，我们使用的是 **Android Studio** 来针对手机客户端进行开发。上面的Intellj IDEA主要可以帮助咱们理解JAVA这个语言的特性和功能，Android Studio则会侧重将Java运用在实际开发中。那我们**冲冲冲**！

同样的，我已经创建过案列了，初始界面不同，但终归后续的步骤都相同。

我们点击右上角的File，然后New，然后New Porject。等等，这一步我们好像见过，emmmmm，和Intellij IDEA的步骤简直一模一样，顿时就亲切了不少。

![](https://z3.ax1x.com/2021/07/29/Wq6Co4.png)



仔细一看连布局都很相似，家人们，回家了！接下来又是激动人心的介绍环节

点击之后会弹出如下页面，我们选择“**Empty Activity**”，之后“**Next**”

![](https://z3.ax1x.com/2021/07/29/Wq66XV.png)



然后我们直接给文件起名就行，这里我用的Demo，选择个人喜欢的位置进行储存，然后**Language**选择**Java**，SDK的话一般都是Andriod4.0以上的版本就行，不需要选择太新的版本。如下图所示

![](https://z3.ax1x.com/2021/07/29/WqcH8s.png)

然后点击Finish完成创建，来到这个页面我们点击Project，选择最下面的Android，就是我们以后常用的开发时的样式了！

![](https://z3.ax1x.com/2021/07/29/Wqg6dU.png)



然后我们看向这里

![](https://z3.ax1x.com/2021/07/29/WqgqFe.png)

![](https://z3.ax1x.com/2021/07/29/Wqgxyt.png)

进入“**AVD Manager**”，看到如下页面,点击“Create Virtual Device”，来创建虚拟机

![](https://z3.ax1x.com/2021/07/29/Wq2kWj.png)

我们会看到一个各种手机手机款式的列表（但应该没有太熟悉的机型），所以我们随机选一个（开心就好）

![](https://z3.ax1x.com/2021/07/29/Wq2y6I.png)

然后Next来到这个界面，为虚拟机选择一个适配的系统吧，这里就直接Android 11.0（因为这个已经下载过了）

![](https://z3.ax1x.com/2021/07/29/WqRpcR.png)

还是Next，进入下面这个页面，然后Finish就Ok了，等待下载

![](https://z3.ax1x.com/2021/07/29/WqR3E8.png)

大功告成，回到我们之前的页面，点击run，在虚拟手机上查看全新的效果吧！

![](https://z3.ax1x.com/2021/08/05/feKjTx.png)

到这里我们学习android所需的基础环境搭建就告一段落，接下来补充一段有关android的布局部分的知识：

我们的布局图画相关文件都在这个res文件夹里，点击后可以看到有四个子文件夹，分别是drawable,layout,mipmap,values。

![](https://z3.ax1x.com/2021/08/05/feMXvQ.png)

其中layout文件夹内存放的就是就是我们布局的相关文件，点开目前唯一一个文件看一下。可以发现和我们刚刚在虚拟机上运行的视图相同。

![](https://z3.ax1x.com/2021/08/05/feQvRO.png)

然后我们点击code，切换到代码部分（也可以点击spilt两边同时看），我们简单介绍一下相关的东西

![](https://z3.ax1x.com/2021/08/05/felItP.png)

* 可以看到这里的控件类型是TextView，也就是文本视图，可以现实其中的文本。同样的控件还有很多如Button（按键），ImageView（图像视图）等。
* 其中会有长度，宽度等必要属性（所有组件都必须有），后面的wrap_content的涵义我们下次会介绍
* 然后就是text这个属性，是控制TextView的文本显示。
* 其他的是和控件的摆放位置相关的属性，这个我们之后也会讲解，有兴趣的同学提前可以下去了解一下

**~小练习~**

***我们怎么通过控件的选择和摆放来实现一个简单的手机计算器的布局样式呢？有兴趣的同学可以尝试一下。具体的教程会在下次直播时与大家见面，敬请期待！***



到这里我们这次的内容就真正结束了，后面会有更多更厉害的学长学姐来丰富上述的案列，带你真正走进Android世界的大门，敬请期待。

希望看到能高举你手中的3连，这对我们很重要，拜托了（~~弹幕发下次一定也行~~）。

![](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fi0.hdslb.com%2Fbfs%2Farticle%2F9879c972f42fafb403c21ac57f68bc7c05b98315.gif&refer=http%3A%2F%2Fi0.hdslb.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1630164393&t=9c8b0c4b1fbbc23f6733c0dd5bbddb13)

想更深入了解我们，你可以关注我们的微信公众账号“桂圈Daily”，里面还有华师校园的日常生活介绍

感谢您的观看，木犀的新朋友，安卓组欢迎你！期待你的加入

<center class="half">
    <img src="https://z3.ax1x.com/2021/08/03/fPblVA.png" width="300"/>
    <img src="https://z3.ax1x.com/2021/08/03/fPbKDH.jpg" width="300"/>
</center>