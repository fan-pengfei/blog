---
title: "buildroot的OVERRIDE_SRCDIR机制"
date: 2023-06-14T02:58:23.000Z
draft: false
tags: ["buildroot"]
---
> 使用buildroot构建系统的话，如果在output/build中对某个软件包修改的话，一旦使用make clean，就会导致自己做的修改被抹除；为避免这个情况，buildroot是提供了一种机制，也即`OVERRIDE_SRCDIR`；
> 参考：[https://www.cnblogs.com/pwl999/p/15534966.html](https://www.cnblogs.com/pwl999/p/15534966.html)

Buildroot的一般操作是下载tar包、提取、配置、编译和安装该tar包内的软件。源代码提取保存在临时目录`output/build/-`目录中，当执行`make clean`时，该目录会被完全删除，并在下一次make时重新创建。即使将Git或Subversion等版本管理系统作为软件包源代码的输入，Buildroot也会从中创建一个tar包，然后像对待一般tar包一样工作。

这种方式非常适合将Buildroot当做集成工具，编译和集成嵌入式Linux系统的所有组件。但是，如果是在开发系统的某些组件的过程中使用Buildroot，这种方式非常不方便：开发者希望对一个软件包的源代码做少许修改，并能够使用Buildroot快速重建系统。
直接修改`output/build/-`不是合适的解决方案，因为该目录会在`make clean`时删除。

因此，Buildroot针对该场景提供了一种特殊的机制，即`_OVERRIDE_SRCDIR`机制。Buildroot读取一个override文档，该文档允许用户告诉Buildroot某些软件包的源代码位置。

覆盖文档(override)的默认位置是`$(CONFIG_DIR)/local.mk`。由`BR2_PACKAGE_OVERRIDE_FILE`配置选项定义。`$(CONFIG_DIR)`是Buildroot `.config`文档的位置，因此`local.mk`默认情况下与`.config`文档放在一起，这意味着：这意味着:

- Buildroot目录树内构建时位于Buildroot顶层目录中（当O=不使用时）

- Buildroot目录树外构建时位于目录树外目录（当O=使用时）

如果需要不同于这些默认值的位置，可以通过`BR2_PACKAGE_OVERRIDE_FILE`配置选项指定。

在这个override文档中，Buildroot期望找到以下形式中的行:

```makefile

_OVERRIDE_SRCDIR = /path/to/pkg1/sources

_OVERRIDE_SRCDIR = /path/to/pkg2/sources
```

例如:

```makefile
LINUX_OVERRIDE_SRCDIR = /home/bob/linux/
BUSYBOX_OVERRIDE_SRCDIR = /home/bob/busybox/
```

当Buildroot发现给定的软件包存在_OVERRIDE_SRCDIR定义时，它将不再尝试下载、提取和修补软件包，它将直接使用指定目录中可用的源代码，并且make clean时不会涉及该目录。这允许将Buildroot指向您自己的目录，该目录可以由Git、Subversion或其他版本控制系统管理。为此，Buildroot将使用rsync将软件包的源代码从_OVERRIDE_SRCDIR指定的位置复制到`output/build/-custom/`目录。

该机制最好与`make -rebuild`和`make -reconfigure`结合使用。make`-rebuild all`将rsync源代码从`_OVERRIDE_SRCDIR`到`output/build/-custom`（只有修改过的文档会被复制），并重新启动这个软件包的构建过程。

在上述Linux软件包的示例中，开发人员可以修改 `/home/bob/linux`目录下的源代码，然后运行：

```bash
make linux-rebuild all
```

并在几秒钟内在`output/images`中获得更新后的Linux内核映像。类似地，可以在`/home/bob/busybox`和后面对BusyBox源代码进行更改:

```bash
make busybox-rebuild all
```

`output/images`中的根文档系统映像包含更新后的BusyBox。

大型项目一般有成百上千的文档，很多文档对于构建时是不需要的，但是会减慢rsync复制源代码的过程。可选的，可以定义`_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS`跳过源代码中的某些文档。例如，当处理webkitgtk软件包时，以下内容将从本地WebKit源代码中排除：

```lua
WEBKITGTK_OVERRIDE_SRCDIR = /home/bob/WebKit
WEBKITGTK_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS = \
        --exclude JSTests --exclude ManualTests --exclude PerformanceTests \
        --exclude WebDriverTests --exclude WebKitBuild --exclude WebKitLibraries \
        --exclude WebKit.xcworkspace --exclude Websites --exclude Examples
```

默认情况下，Buildroot会跳过VCS信息（例如.git或.svn）的同步。一些软件包在编译过程中会使用VCS信息，例如精确确认提交信息。要取消Buildroot的内置过滤规则，需要重新添加以下目录：

```makefile
LINUX_OVERRIDE_SRCDIR_RSYNC_EXCLUSIONS = --include .git
```
