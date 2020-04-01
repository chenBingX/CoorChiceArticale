

# 既生 Android，何生 Flutter

Fuchsia 是一个大一统的操作系统，智能手机、平板、电脑、智能音响、车载系统、或者你的智能牙刷



核心：Zircon Microkernel



Fuchsia 提供了统一的系统升级功能，Android 不能。

[Fuchsia Google git](https://fuchsia.googlesource.com/?format=HTML)

[Fuchsia 官网](https://fuchsia.dev/)

Fuchsia also offers a Vulkan-based graphics rendering engine called Escher

Fuchsia 将会提供一个特殊的版本，能够以虚拟机的方式运行 Android 应用。


Fuchsia 的渲染库是比 **OpenGL** 更强大的 **Escher**，它是基于 **Vulkan** 渲染库开发的。

在 Escher 的基础上，构建了 Scenic 图形引擎，未来 Flutter 将能够通过该引擎进行渲染。

Fuchsia will cover all of these bases. It sure doesn’t look like it will be powering servers, or become a system of choice for developers, video editors or hardcore PC gamers — in other words, it won’t replace Windows. But for users who spend most of their time browsing the web, writing and consuming multimedia, it may be just perfect. Don’t you see the gap between the simplicity of Android and the capabilities of Windows? Google and Microsoft do, and they’ve already tried to fill it in. Unfortunately, Chrome OS and Windows 10S turned out to be too limited.


It all sounds very compelling to users. But let’s be honest — turning the mobile world upside down can’t be all about excellent user experience. There has to be something in it for Google. And what exactly? Well, the keyword here is control. First of all, Fuchsia is supposed to fix a few internal Google problems. It ditches the Linux kernel for one created internally by Google. Without going into technical details — it will give Google more control over significant changes in the OS and the software updates. Besides, Fuchsia will be able to run Swift code, and it already uses Flutter, which can make this system way more developer-friendly and better optimized than Android is. Porting apps from Android to Fuchsia will be a fast and smooth process.
这听起来对用户来说非常有吸引力。但是，说实话，颠覆移动世界并不能完全依靠出色的用户体验。 Google必须有其中的东西。到底是什么？好吧，这里的关键词是控制。首先，紫红色应该可以解决Google内部的一些问题。它会为Google内部创建的Linux内核挖沟。无需深入探讨技术细节-它将使Google对操作系统和软件更新的重大更改拥有更多控制权。此外，Fuchsia将能够运行Swift代码，并且已经使用Flutter，这使得该系统比Android更加易于开发和优化。将应用程序从Android移植到紫红色将是一个快速，平稳的过程。


And most importantly: the ecosystem. Right now, the success of many Google services is dependent on Windows. Would you want your success to be in the hands of your competitor? Not really. Microsoft is trying to push Office instead of Google Docs, Bing instead of Google Search, etc. Many casual users don’t even think about choosing something else over the default solutions — that’s why Google pays Apple billions of dollars a year just to be the default search engine on iPhones. With Fuchsia, the company could pluck up some of those casual users from Microsoft, and close them in its ecosystem on multiple devices.
最重要的是：生态系统。目前，许多Google服务的成功取决于Windows。您希望您的成功掌握在竞争对手的手中吗？并不是的。微软试图推动Office而不是Google Docs，必应（Bing）而不是Google Search等。许多临时用户甚至没有考虑在默认解决方案上选择其他东西–这就是为什么Google每年仅向Apple支付数十亿美元给苹果iPhone上的默认搜索引擎。借助Fuchsia，该公司可以吸引一些来自Microsoft的临时用户，并在多种设备上关闭其生态系统。

Fuchsia 可以运行 Swift




也许 Google 在谋划一盘大棋。
基于上一代技术的产业，他们转变升级，会造成成本的巨大压力，原有的沉淀积累、人才储备都需要升级或者革新。

作为虚拟产业入口的操作系统，在移动端主要有 iOS，虽然市场份额 Android 要高出不少，但 Android 诸多与生俱来的性能体验、安全性都是全世界用户都有共识的，不如 iOS。

苹果设备目前售价比较高，是一道挡在消费者前面的门槛，如果苹果那天变脸不端着架子，铁了心要抢占市场份额，售价降下来，摆在消费者面前1999 的iOS和 1999 的Android 怎么选？实际上这两年苹果应该也有在越界的边缘试探过，推出经济版的设备。

但是苹果走到今天这一步，也不是他想降价就能立马降下来的。

但对 Google 来说，这一定会是一个很大隐患。PC 端有早已制霸多年的 Windows，MacOS 也占有一部分市场，Google 可以说是基本被排除掉了。

这部分市场 Google 作为一家创新型的科技公司就真的甘心不分一地，不占一城吗？应该是不可能的，占住操作系统，才是把控住了整个产业入口，才能成为游戏规则的制定者。

Fuchsia 操作系统可能才是 Google 对接下来竞争的押注。不是在什么时候都有机会去做统一、颠覆之类的事情的，趁着万物互联的物联网，Google 是有这个机遇去超车。新游戏，虽然可能起点不一样，但是努把力、找到更优路径，是完全有机会改变格局的。

很可能是这样的，Fuchsia 系统的成功与否，决定者在下一个时代，Google 在产业中的话语权和是否能成为规则制定者。

一套操作系统，最主要的外在部分就是软件生态了。足够多，足够全的软件生态才能对用户产生粘性。随着 Fuchsia 这两年一直时不时的对外放出消息，一方面是吸引市场产业的关注，另一方面是想试探市场产业对此的反应，以便对后续的推出策略进行调整。

虽然 Fuchsia 一直宣称是面向物联网的，但是这两年放出的演示Demo不是移动设备就是PC，说 Google 不在意接下来的巩固移动和抢夺PC市场是证据不足的。


Flutter 作为 Fuchsia 的 GUI 框架基本上是板上钉钉，在 Flutter 的平台判断源码中，已经给 Fuchsia 留好了坑位了。

这两年 Google 花费了很多资源来为 Flutter 做宣传，教育开发者，吸引企业参与。这一点国内的各大互联网企业还是很认 Google 老大哥的牌子的，确实基本上都或多或少的参与进去了。不管是新起的应用直接使用 Flutter，或是主力 App 使用Flutter 来对老业务改造，都说明 Google 这两年的钱没白砸，一波接一波的洗闹宣传还是很成功。起码目前来看，Google 的步伐还是稳扎稳打的，我相信目前的完成度应该都是符合 Google 预期的。


我认为值得注意的是，Google 这次推出的 Flutter 绝对不是之前一些跨端解决方案那样，由企业某个团队孵化，只是想解决跨端问题，收割点用户或技术影响力之类。而是想为未来铺路的，是有关联性的。

Fuchsia 系统推出后，前期最大的问题就是软件生态为0。那为什么 Google 不直接兼容现有应用呢？这样系统就需要做大量的改造兼容，不纯粹。如果反过来看，这跟在老系统上扩展出个新系统有什么区别？盖新房的时候肯定是把老房推了从头盖的好，长期来看成本和风险都会更小。

WindowsPhone 就是走的不稳，0生态推出，想凭借影响力和 PC 带来的话语权培养软件生态，最后发现大家是不买账的，没有企业、开发者或者愿意在 Android、iOS 之外再为第三个系统开发。这样没软件就没用户，没用户就没人愿意给它开发软件。窗口哥还是高估了自己影响力和话语权。


Flutter 现阶段最大的 KPI 应该就是推动尽可能多的新应用直接使用 Flutter 进行开发，而老应用尽可能多的改造成 Flutter。这一步走的好，Fuchsia 一推出，软件生态就是完备。反正 Flutter 能在 Android 上跑，能在 iOS 上跑，能在 Fuchsia 上跑，也能给厂商们一个系统切换的缓冲。

现在有个更好更流畅，和其它平台统一性更好，各方面都更好的系统，看你们切不切？放心切的话也可以一步步来，Flutter 应用在原来的系统 和 Fuchsia 中体验都是一致的，不用担心用户培养问题。移动市场巩固好了，如果 Fuchsia 真的有很强的统一性，那将手伸向 PC 平台也是很容易的事。


Fuchsia 能不能把 iOS 的市场进一步压缩不好说，但稳定目前的份额，应该是问题不大的，毕竟是没有历史包袱的更先进的系统。而 Android 我认为很大程度的可能是会被 Fuchsia 逐步替换掉的。因为它显然不在 Google 未来的大一统格局中。

而且目前在 GUI 层面，Flutter 已经基本对齐 Android 了，在推出 Fuchsia 后，一个完整的操作系统的其它部分也会得到补充，那还有什么必要留着 Android 呢？


所以现在 Google 鼓吹 Flutter 是希望各个应用厂商能逐步开始沉淀积累，以便后续能有平稳的过渡，同时也是为自己培养好生态。


我觉得不用太在意 Flutter 的跨端能力部分，那只是一个附属。Google 未来的蓝图决定了 Flutter 不是大公司内部的娱乐项目，而是未来蓝图中的一个部分。


我觉得，未来一定是要模糊端的概念的，手机、平板、PC或者其它设备，对任务的处理的连续性是绝对的大趋势。想想看，我们听歌、电话、写文章、使用软件，能几乎无缝的在各个平台（我所指的不仅仅是局限于手机、平板、PC，还可以是其它设备）间切换延续，是多科幻的事。而 Android 显然与这样的布局是格格不入的。

对苹果而言，在 Google 这样的布局下，是否有能力和 Google 叫板，说用 Flutter 就不能上架呢？

我认为还是有风险的，毕竟如果 Fuchsia 系统的表现能和 iOS 一样，且互联能力和苹果一样或者比苹果更好，而且价格也更友好，那苹果就真的丧失竞争力了，更别提要把 Flutter 拒之门外了。