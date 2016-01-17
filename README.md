### 写自己的开源库，发布到 JitPack.io

博客地址首发：[http://icodeyou.com/2015/12/23/2015-12-23-AndroidLibraryJitPack](http://icodeyou.com/2015/12/23/2015-12-23-AndroidLibraryJitPack)

之前一直在用别人的开源库，尤其有了 Gradle 后，依赖别人的开源库就更简单了，只需要在项目的 build.gradle 文件中添加上类似 `compile 'com.github.icodeu:CommonAdapter:v1.0'` 的就好了，剩下的就可以交给 Gradle 帮助我们完成 library 依赖了。但这个是怎么做的，如果你也想学习怎么写自己的 library，然后能够提供给别人使用的话，可跟着本篇博客学习一下。本篇分为两个部分，首先讲解如何单纯的写个 library 并在本地自己依赖着(玩)，然后进入主题讲解怎么使用 JitPack.io 来发布我们自己的 library，所以如果你已经知道了怎么写 library，可以直接跳到第二部分。

我也试图在网上找一些关于使用 JitPack.io 的教程，但是搜出来的基本是[这篇](http://www.tuicool.com/articles/Vv6jEb)的复制品，根本没讲什么实质性的东西，所以一切只好自己来了。

## 如何写 library

在本节，我们一起来新建个 library，打开 Android Studio，New Project，在此工程名为 Hellolibrary，此后一直点击 next 直至 finish 即可。此时应该是只有一个 module 名为 app，一会我们会把它当做依赖 library 的示例模块来使用，暂时先不管，我们先来创建一个 library，File->New->Module，选择 Android library，之后起个名字，在此叫做 library，finish 即可，此时工程目录如下所示，其中 app 用来依赖使用 library。

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack00.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack01.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack02.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack03.png)

怎么指定一个 module 到底是普通的 application 还是 library 呢？打开 module 对应的 build.gradle 文件，看第一行 `apply plugin: xxx`，如果是 application 的话，plugin 就是 'com.android.application'，library 的话就是 'com.android.library'，就这么简单，如下图：

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack22.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack23.png)

接下来在 library 中 New->Java Class，在此就是演示，所以我们就尽可能把不重要的东西简单化，名字就叫做 CustomUtil，只有一个静态方法，如下图所示：

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack04.png)

其实到此最简单的 library 就写好了，下面就在 app 中来依赖刚才的这个 library 看看效果。打开 app 的 build.gradle，在最后的 dependencies 节点添加一行，`compile project(':library')`，再 Sync 一下 Gradle即可，这样 app 就完成了对 library 的依赖。另外还有一种方法去依赖 library，右键 app 这个 module，选择 Open Module Settings，切换到最后一个Tab:Dependencies，点击左下角的加号->Module dependency->选择 library，这样也能完成 app 对 library 的依赖。

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack05.png)

那怎么能在代码上看出来 app 确实成功依赖了 library 呢？我们打开 app 中的 MainActivity，输入 CustomUtil 试一下，如果发现像下图那样给你智能提示了，那就OK了，从下图还能看到 CustomUtil 类所在的包是 com.icodeyou.library。我们调用一下它吧，如下图也很简单，不解释了。

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack06.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack07.png)

到此为止我们就自己写了一个最简单的 library 并完成了自己本地的依赖调用，这都是自己玩玩，那怎么能让别人也用上咱们刚才那个高大上的 library 呢，继续往下看。


## 如何在 JitPack.io Publish Android Library

首先来概览一下我们要完成的最终效果是什么，我之前有一个开源库，地址在 [https://github.com/icodeu/CommonAdapter](https://github.com/icodeu/CommonAdapter)，打开 JitPack.io 的网站，将我的开源库地址粘贴进去，Look up->Get it，即可看到熟悉的 compile 了，按照下面那个提示复制到相应的 build.gradle 文件中就可以完成 library 的依赖了，不信你试试啊。

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack08.png)

下面就来具体讲解一下如何将刚才本地的 library 与 JitPack.io 结合起来，JitPack 的官方文档在这里 [Publish Android library](https://jitpack.io/docs/ANDROID/)

打开 Project 的 root build.gradle，在 dependencies 节点添加一个 classpath：`classpath 'com.github.dcendents:android-maven-gradle-plugin:1.3'`，再打开 library 的 build.gradle，在文件起始添加如下两行 `apply plugin: 'com.github.dcendents.android-maven'` `group='com.github.icodeu'`(注意这是你GitHub的用户名)，两个文件的示意图如下：

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack09.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack10.png)

下面检查你的Project目录是否存在 gradle/wrapper/gradle-wrapper.jar、gradle-wrapper.properties 这两个文件，如果存在可跳过下面这步，如果不存在，请按下面的进行操作。打开当前项目的 Terminal，先后执行 `gradle wrapper` 和 `./gradlew install` 两个命令，如下图：

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack11.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack20.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack21.png)


从 JitPack 首页也能看出来，其实我们是要粘贴一个 GitHub 项目地址进去，所以我们接下来就要把 library 先上传到 GitHub 上面。

在 GitHub 上新建一个仓库，这里命名为 HelloLibrary，之后 clone 到本地，再把 Project 目录所有的文件复制进去，比如在我这里，仓库 clone 到了桌面的 HelloLibrary 文件夹，如下图：

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack12.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack13.png)

把这些文件 push 到 GitHub 上，push 成功之后，点击项目的 releases->Create a new release，填写好版本号等内容，如下图：

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack15.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack16.png)

填写好后点击 Publish release 即可，然后我们复制下图红框中的项目地址，粘贴到 JitPack.io 上面，Look up->Get it，在下面就能看到使用方法了。

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack17.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack18.png)

首先打开 Project root 的 build.gradle，在 repositories 节点添加上 `maven { url "https://jitpack.io" }`，之后打开想依赖这个 library 的模块，比如这里我们是 app 这个 module，在 dependencies 节点添加上 `compile 'com.github.icodeu:Hellolibrary:v1.0'`，Sync 一下 Gradle，这样就可以了。如果 Sync 成功了，那我们在 app 中就成功依赖了 GitHub 上的 library，此时可以打开 app 的 Module Settings，看一下它的 Dependencies，如下图：

![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack24.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack25.png)
![img](http://7xivx9.com1.z0.glb.clouddn.com/jitpack19.png)

好了，到此就完成了使用 JitPack.io + GitHub 发布开源库了，相比 jCenter 和 MavenCentral 方式简直爽的不要不要的
