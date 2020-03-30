# Google Fuchsia

Fuchsia is an open source capability-based operating system currently being developed by Google. 
Fuchsia是Google目前正在开发的基于开源功能的操作系统。

It first became known to the public when the project appeared on a self hosted form of git in August 2016 without any official announcement.
当该项目于2016年8月以自托管形式git出现而没有任何正式公告时，它首次被公众了解。
 
 The source documentation describes the reasoning behind the name as "Pink + Purple == Fuchsia (a new Operating System)", which is a reference to Pink (Apple's first effort at an object-oriented, microkernel based operating system), and Purple (the original iPhone's codename).
原始文档将名称背后的原因描述为 “Pink + Purple ==紫红色（新的操作系统”，它引用了Pink（Apple首次致力于基于对象的，基于微内核的操作系统），和Purple（原始iPhone的代号）
Fuchsia 名字的由来是 "Pink + Purple"。Pink 名字的由来是 Apple 首次尝试开发的微内核系统，当然，没有成功；Purple 是初代 iPhone 的代号。

In contrast to prior Google-developed operating systems such as Chrome OS and Android, which are based on the Linux kernel, Fuchsia is based on a new microkernel called Zircon, named after the mineral.
与之前的谷歌开发的基于Linux内核的Chrome OS和Android等操作系统不同，Fuchsia 基于名为Zircon的新微内核（以矿物命名）。
  

The GitHub project suggests Fuchsia can run on many platforms, from embedded systems to smartphones, tablets, and personal computers. 
GitHub 项目暗示 Fuchsia 可以在许多平台上运行，从嵌入式系统到智能手机，平板电脑和个人计算机。


In May 2017, Fuchsia was updated with a user interface, along with a developer writing that the project was not a "dumping ground of a dead thing", prompting media speculation about Google's intentions with the operating system, including the possibility of it replacing Android. 
在2017年5月，Fuchsia 用户界面得到了更新，开发人员写道描述这不是 "dumping ground of a dead thing"，这引发了媒体对 Google 操作系统意图的猜测，包括其取代 Android 的可能性。


On July 1, 2019 Google announced the homepage of the project, fuchsia.dev, which provides source code and documentation for the newly announced operating system.
2019年7月1日，Google 公布了该项目的主页 fuchsia.dev，该网站提供了新发布的操作系统的源代码和文档。



In November 2017, initial support for the Swift programming language was committed.
2017年11月，承诺对Swift编程语言提供初步支持。

In January 2018, Google published a guide on how to run Fuchsia on Pixelbooks.This was done successfully by Ars Technica.
2018年1月，谷歌发布了关于如何在Pixelbooks上运行紫红色的指南，由 Ars Technica 成功完成。

A Fuchsia "device" was added to the Android ecosystem in January 2019 via the Android Open Source Project (AOSP). Google talked about Fuchsia at Google I/O 2019.Hiroshi Lockheimer, Senior Vice President of Chrome and Android, described Fuchsia as one of Google’s experiments around new concepts for operating systems.

On July 1, 2019, Google announced the official website of the development project providing source code and documentation for the operating system.
Google在2019年7月1日宣布了该开发项目的官方网站，该网站提供了操作系统的源代码和文档。

Features
Fuchsia's user interface and apps are written with Flutter, a software development kit allowing cross-platform development abilities for Fuchsia, Android and iOS. 
Fuchsia的用户界面和应用程序均使用Flutter编写，Flutter是一种软件开发套件，允许针对Fuchsia，Android和iOS进行跨平台开发。


Flutter produces apps based on Dart, offering apps with high performance that run at 120 frames per second.
Flutter生产基于Dart的应用，可提供以120帧/秒的速度运行的高性能应用。
 
Fuchsia also offers a Vulkan-based graphics rendering engine called Escher, with specific support for "Volumetric soft shadows", an element that Ars Technica wrote "seems custom-built to run Google's shadow-heavy 'Material Design' interface guidelines".
Fuchsia 还提供了一个基于 Vulkan 的图形渲染引擎（一个高性能的图形渲染引擎，比 OpenGL 要高），称为 Escher，它特别支持“Volumetric soft shadows”，Ars Technica 写道“该元素似乎是为运行Google的重阴影“材料设计”界面指南而定制的。

Due to the Flutter software development kit offering cross-platform opportunities, users are able to install parts of Fuchsia on Android devices. Ars Technica noted that, while users could test Fuchsia, nothing "works", adding that "it's all a bunch of placeholder interfaces that don't do anything", though finding multiple similarities between Fuchsia's interface and Android, including a Recent Apps screen, a Settings menu, and a split-screen view for viewing multiple apps at once.
由于Flutter软件开发套件提供了跨平台的机会，因此用户可以在 Android 设备上安装 Fuchsia 的一部分。 Ars Technica 指出，尽管用户可以测试 Fuchsia，但没有任何“效果”，并补充说，“这是一堆占位符界面，什么也不做”，尽管发现 Fuchsia 的界面和 Android 之间存在多种相似之处，包括“最近使用的应用程序”屏幕，设置菜单和一个分屏视图，可一次查看多个应用程序。

After the second review, Ars Technica experts were impressed with the progress, noting that things were then working, and were especially pleased by the hardware support. One of the positive surprises was the support for multiple mouse pointers.
在进行第二次审核后，Ars Technica 的专家对该进展印象深刻，并指出当时一切正常，并且对硬件支持感到特别满意。令人惊讶的惊喜之一是对多个鼠标指针的支持。


A special version of Android Runtime for Fuchsia will be developed. It will run on machines with this system from a FAR file, the equivalent of the Android APK.
将开发适用于 Fuchsia 的 Android Runtime 的特殊版本。它将在 FAR 文件（相当于Android APK）中在装有该系统的机器上运行。

# Zircon(锆石)

Fuchsia is based on a new microkernel called Zircon. 

Zircon is derived from Little Kernel,  a small operating system intended for embedded systems.
Zircon源自Little Kernel，Little Kernel是旨在用于嵌入式系统的小型操作系统。

"Little Kernel" was developed by Travis Geiselbrecht, a creator of the NewOS kernel used by Haiku. 

Forbes describes the evolution of Zircon as 

Zircon was previously known as Magenta and it was designed to scale to any application from embedded RTOS (Real-time operating systems) to mobile and desktop devices of all kinds. 
Zircon 以前被称为 Magenta，它的设计目的是可以扩展到从嵌入式 RTOS（实时操作系统）到各种移动和台式设备的任何应用程序。

As a result, there has been much speculation that Fuchsia will be the natural successor to Android and Chrome OS, combining capabilities of both with backwards compatibility to run legacy applications built on either. 
因此，人们一直猜测Fuchsia将成为Android和Chrome OS的自然继承者，将两者的功能与向后兼容性相结合，可以运行基于二者之一的旧版应用程序。

In short, this thing is designed to run on anything from 32-bit or 64-bit ARM cores to 64-bit X86 processors and it has a potential to be rather disruptive.
简而言之，该工具旨在在从32位或64位ARM内核到64位X86处理器的任何设备上运行。

The software documentation of the Zircon microkernel outlines the technical specifications. 
Zircon 微内核的软件文档概述了技术规格。

Zircon comprises a microkernel (source in kernel/...) and userspace services, drivers, and libraries (source in system/...) as part of the boot process as well as talk to hardware. Zircon provides more than 100 syscalls. 

Zircon syscalls are generally non-blocking with the wait_one, wait_many port_wait and thread sleep being the notable exceptions.
Zircon系统调用通常是非阻塞的，其中 `wait_one，wait_many、port_wait` 和线程睡眠是最明显的例外。
