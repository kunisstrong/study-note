![2022 年 Flutter vs React Native](https://cdn-1.backendless.com/wp-content/uploads/2022/01/Flutter-vs-React-Native-in-2022.png)

随着移动应用程序开发越来越受欢迎，企业正在寻找方法来创建可在各种设备上使用的跨平台应用程序。

当我们说跨平台时，我们当然指的是Android和iOS。根据[Statista](https://www.statista.com/statistics/272698/global-market-share-held-by-mobile-operating-systems-since-2009/)：

*Android 在 2021 年 6 月保持了其作为全球领先移动操作系统的地位，以接近 73% 的份额控制着移动操作系统市场。Google 的 Android 和 Apple 的 iOS 共同占据了全球 99% 以上的市场份额。*

在本文中，我们将比较两个流行的跨平台开发框架：Flutter 和[React Native](https://reactnative.dev/)。我们将研究每个框架的优缺点，并讨论哪个更适合在 2023 年使用。

## 为什么移动应用程序开发如此受欢迎？

开发移动应用程序是一个稳步增长的业务领域。几乎地球上所有人都拥有手机，这意味着潜在用户的数量几乎是无限的。因此，现在几乎所有东西都有应用程序。

您可以选择多种方式来设计和构建应用程序。您可以使用本机方法，例如 Swift 和 Objective-C 来创建 iOS 应用程序和 Java 来创建 Android 应用程序。这些是官方的 Apple / Google 软件编程语言，分别提供支持和经常更新的功能。

或者，您可以使用跨平台框架，例如 Flutter 或 React Native。

## 什么是跨平台应用程序开发？

在我们开始之前，让我们定义术语“跨平台应用程序开发”并将其分为两类：混合开发和本机开发。

跨平台应用程序是指可以使用单一代码库开发并且在 iOS 和 Android 操作系统上功能几乎相同的应用程序。（在本文中，我们专注于移动应用程序开发）

### 混合开发

混合应用程序是结合 HTML5、CSS 和 JavaScript 等网络技术开发的。这意味着混合应用程序跨平台共享一些代码（例如 HTML/CSS/JS 代码），并且此共享代码在目标平台上的 webview 中运行。

WebView 应用程序是使用嵌入式 webviews 呈现其用户界面的混合应用程序，您可以在其中使用 HTML5、CSS 和 JavaScript 进行自定义。

WebView 应用程序在开箱即用地访问设备 API 方面会有一些限制，需要额外的努力才能实现与本机应用程序相同的某些功能。权衡是这些应用程序是开箱即用的跨平台，这可以节省大量时间。

混合应用程序在两个平台上可能看起来相同但行为不同，这取决于它们可用的特定于平台的 API。例如，天气应用程序会检查两个平台上当前位置的天气服务的 API，并根据每个平台上可用的内容返回不同的数据。

### 原生开发

本机应用程序是使用其目标平台（例如 Android 或 iOS）的本机 SDK 开发的。这意味着它们不跨平台共享任何代码，并且此共享代码仅为目标平台编写，而 UI 是使用特定于平台的小部件和库实现的。

原生应用提供了比混合应用更好的用户体验，并且在每个平台上看起来更原生，但由于开发人员需要时间学习目标平台的 API，它们的开发成本更高，发布新功能的时间也更长。

一般来说，最好使用目标平台的原生开发工具（例如 Android Studio 或 Xcode）来开发您的应用程序。

### Flutter 和 React Native：跨平台框架

Flutter 和 React Native 都是当今最好的跨平台开发框架之一。他们都使用本机小部件来提供高度可定制、响应迅速的 UI，同时跨不同平台共享代码。

Flutter 框架由谷歌开发，而 React Native 框架由 Facebook 开发，因此这些科技巨头拥有非常庞大的团队来处理从平台的 SDK 到文档、支持等一切事务。

虽然 Flutter 和 React Native 应用程序都是原生的，但它们优于传统的原生应用程序开发，因为它们可以跨平台共享很大一部分代码库。

[据 Instagram 称](https://engineering.instagram.com/react-native-at-instagram-dd828a9a90c7#.8wdmfmhep)，iOS 和 Android 之间通过 React Native 功能共享的代码量超过 90%。

## Flutter 和 React Native 的历史

Flutter 是在 2016 年 10 月的 Dart 开发者峰会上宣布的。这个跨平台移动应用程序开发框架背后的主要思想是为开发人员提供工具，以使用用 Google 自己的 Dart 编程语言编写的单一代码库为 iOS 和 Android 构建原生应用程序。

Flutter 的第一个稳定版本 (1.0) 于 2018 年 2 月 16 日发布。

React Native 的起步时间比 Flutter 早一点，2015 年 3 月发布了第一个 beta 版本，但直到 2017 年 3 月才离开 beta 阶段。2017 年 9 月 5 日，React Native 在技术人员的大力支持下达到了 1.0 版巨人脸书。

正如您所见，这两种技术都相对较新，但React Native 已经被 Facebook、Instagram、Airbnb 和 Uber 等大公司使用。

[![苹果应用商店上的脸书](https://cdn-3.backendless.com/wp-content/uploads/2022/01/Facebook-on-Apple-App-Store.jpg)](https://apps.apple.com/us/app/facebook/id284882215)

Flutter 也已经被宝马、丰田、eBay，当然还有谷歌自己的 Google Pay 所接受。



## Flutter 和 React Native 的比较

Flutter 和 React Native 的主要区别在于 React Native 不会编译成原生移动语言（Java、Swift、Objective-C），而是简单地运行其JavaScript代码。另一方面，Flutter 将其 Dart 语言编译为原生语言，这会影响性能（稍后讨论）。

![Javascript 与飞镖](https://cdn-3.backendless.com/wp-content/uploads/2022/01/Javascript-vs-Dart-600x280.png)

另一个很大的区别是原生 JavaScript（JSX 除外）用于在 React Native 上编写组件。由于 React 的动态特性，Facebook 开发人员建议在使用 React 时使用 Flow 或 TypeScript。对于 Flutter，Google 也推荐使用 Dart 来编写代码，因为它具有静态类型系统。

在我们看来，选择这两种技术中的哪一种应该更多地取决于您的偏好，而不是它们的实际特性和功能。

当然，了解所有编程范例是件好事，这样您就可以轻松地掌握新的语言和框架，即使它们不是用您熟悉的语言编写的。

已经熟悉 JavaScript (ES2015+) 或 TypeScript/Flow 的程序员会发现使用 React Native 开始工作要容易得多。对于使用 React 开发 Web 的开发人员来说尤其如此，因为 React 和 React Native 之间有很多重叠之处。

JavaScript 在移动开发领域仍然占有重要的市场份额，而且由于 React Native 和 Flutter 都允许您选择自己喜欢的编程语言，因此很难说哪一种在 2023 年更受欢迎。

还有其他重要因素可能会影响此决定，例如：

– 公司对特定技术栈的偏好

– 开发人员对给定语言/框架的熟悉程度

– 具备使用特定技术所需技能的开发人员的可用性

正如我们已经指出的那样，React Native 和 Flutter 都将在 2023 年成为重要的参与者，因此您可以选择哪个是您长期目标的最佳选择。

## 跨平台开发框架的工作原理

![跨平台应用](https://cdn-1.backendless.com/wp-content/uploads/2022/01/Cross-platform-apps-600x303.png)

尽管跨平台移动开发框架共享许多概念和功能，但它们在创建时都考虑到了不同的目标。

React Native 旨在提供本机代码性能，并结合 React web 带来的开发便利性。这个想法不是在 iOS 和 Android 之间使用一组共享组件，而是将完全独立的 UI 包装到一个 JavaScript 包中，允许您在一个地方发布几乎一半的应用程序代码。

Flutter 的创建主要是为了满足谷歌对……谷歌的需求。换句话说，这是将快速开发周期与本机代码性能结合起来并构建可在 iOS 和 Android 应用程序之间共享的可重用 UI 组件的另一种尝试。这就是 Google 的 Flutter 比 React Native 快得多的原因。

Flutter 的设计理念也是让应用程序开发更容易、更易于访问，因为它允许使用 Dart 编写代码，Dart 是一种可以在周末学习并在几天甚至几小时内掌握的语言，具体取决于开发人员的技能。

这就是为什么我们相信 Flutter 将成为需要在不牺牲性能或功能的情况下快速创建大量原生移动应用程序的公司的首选移动开发框架。

## 构建移动应用再次变得有趣

当谷歌宣布推出 Flutter 时，开发人员惊讶于它在实践中与其他专门为构建跨平台移动应用程序而开发的技术相比表现出色。

React Native 在 iOS 和 Android 之间共享 UI 代码的理念是一个伟大的创举，但由于 React Native 的固有局限性，由此产生的应用程序无法像原生应用程序那样执行。

Flutter 带有许多您今天在任何其他工具中都找不到的好东西。Dart 是一种令人印象深刻的语言，它是为创建移动应用程序而从头开始构建的。

Dart 是目前可用于构建 Android 和 iOS 应用程序的最快语言，可以更轻松地构建高性能 UI 组件，具有强大的 IDE（集成开发环境）支持和强大的自动完成功能，允许在不丢失应用程序状态的情况下进行实时编码原型设计，并最终拥抱面向对象的编程，使其成为强制性的。

Flutter 带有一个完整的工具链和一组漂亮的类似 Material Design 的小部件，开发人员可以在他们的应用程序中重复使用这些小部件。谷歌还创建了许多与第 3 方库的集成，例如用于以有效方式处理图像的图像处理库、SQL数据库（通过抽象可访问）和文本编辑器。

所有这些都作为一个内聚包呈现给开发人员，该包在设计时考虑了速度、易用性、生产力和性能。

## Flutter 和 React Native 应用的优缺点

使用 Flutter 创建的应用程序与原生应用程序没有区别。它们具有相同的性能和相同的外观（除了一些特定于平台的风格方面）。

人们在使用 React Native 构建应用程序时通常抱怨的主要问题与其运行时环境有关，这比为每个架构管理单独的进程更重。这意味着您将无法使用 React Native 实现纯原生应用程序的性能，尽管您可以接近。

在支持现有 JavaScript 代码库和允许重用 iOS 和 Android 应用程序之间共享的某些组件方面，Flutter 没有与 React Native 相同的优势。

现在，让我们更深入地探讨一下这两个框架的技术优缺点。

![Flutter 和 React Native 的优缺点](https://cdn-1.backendless.com/wp-content/uploads/2022/01/Flutter-and-React-Native-Pros-and-Cons.png)

### 本机性能方面的优缺点

React Native 带有改进的 JavaScript 虚拟机，由于其 JIT 编译器，它比 V8 更快。它还受益于作为一个提前编译的框架，这意味着你可以自由地发布你需要的任何代码库，因为它会被编译成一个本地可执行文件。

实际上，React Native 与纯原生应用程序一样快，因为它可以实现与 iOS 应用程序相同的性能，而无需对 iOS 构建设置进行任何更改。

Flutter 带有自己的 Ahead-of-time 编译器，一旦您构建了项目，它将为 iOS 和 Android 生成优化的代码。您无需像 React Native 那样在应用程序二进制文件中交付整个代码库即可获得本机性能。

### 应用程序大小的优缺点

React Native 应用程序通常带有一个 JavaScript 运行时，压缩后重约 300kb，尽管可以通过调整一些选项来减少这个数字，例如绕过填充（这将强制 React Native 跳过用结果填充其虚拟 DOM 的过程将其与本机 UI 进行比较）以及将 useDeveloperMode 设置为 true（这将调整内存中的图像大小并降低其质量）。

Flutter 带有一个提前编译器，它允许开发人员只发布他们正在构建的应用程序所需的代码库，而无需与它捆绑任何东西。

如果您愿意，可以在现有的 JavaScript VM 中运行 Flutter，这将允许您节省应用程序所需的空间。

### 最低要求的 SDK 版本的优缺点

React Native 通常可以毫无问题地针对任何 iOS 9+ 或 Android 5.0+ SDK 构建，但不用说，要获得最佳性能，您应该以发布时可用的最新 SDK 版本为目标。

在实践中，React Native 应用程序可以针对较旧的 iOS 和 Android SDK 构建，在运行时只提供有限的功能集，尽管要获得所有功能，您仍然应该以可用的最新 SDK 版本为目标。

Flutter 应用程序可以针对 Android 版本 21 (Lollipop) 和更新版本构建，但建议针对可用的最新 SDK 版本构建以获得最佳性能。

Flutter 可以在 iOS 8 或更高版本上运行，但调用某些 API 可能会导致运行时崩溃，因为 Apple 已经弃用了 Flutter 使用的大部分 API。

### UI开发的优缺点

Flutter 自带一组用于呈现 UI 的小部件，这意味着您可以在构建 Flutter 应用程序时重用现有的 iOS 或 Android 代码。

一些第三方库可用于更轻松地重用现有的本机组件，尽管这仍在进行中，因为将 Flutter 小部件映射到现有的 iOS 和 Android UI 组件并不容易。

React Native 带有一个桥接器，允许您将现有的 iOS 和 Android 代码重用为 JavaScript 模块，并公开一些 API 以手动创建本机 UI 组件和处理渲染它们的 JavaScript 代码之间的桥接器。

### 调试方面的优缺点

React Native 带有自己的调试器，可以附加到您在 iOS 和 Android 上运行的应用程序，它为开发人员提供了 JavaScript 虚拟机当前状态的预览，以及用于检查内存使用情况或动态调整某些选项的各种工具.

Flutter 也有自己的调试器，它可以附加到你在 iOS 和 Android 上运行的应用程序，为开发人员提供渲染引擎当前状态的预览，以及用于检查内存使用或动态调整某些选项的各种工具.

### 移动平台之间代码重用的优缺点

React Native 带有自己的一组 API，可在开发 iOS 和 Android 时使用。虽然大多数使用 React Native 的公司会先在一个平台（通常是 iOS）上开发他们的应用程序，然后再将它们移植到另一个平台，但如果您愿意，也可以在 iOS 和 Android 应用程序之间编写共享组件。

Flutter 应用程序是使用特定于平台的代码构建的，因此无法在 iOS 和 Android 应用程序之间共享任何代码。但是，可以使用第三方库来更轻松地重用现有的本机组件。

## Flutter 和 React Native 哪个更容易学？

React Native 和 Flutter 都同样容易学习（就 API 而言），尽管这将取决于开发人员的专业知识。两者都有一个庞大且参与度高的开发人员社区，可以帮助新的开发人员并不断创建新的工具和组件。

对于编码经验很少或没有编码经验的全新开发人员，我们可能会建议从 React Native 开始，因为它带有一组可用于构建 iOS 和 Android 应用程序的预定义组件，这意味着您可以一次学习一个东西无需担心学习用于呈现视图的所有 API。

但是，我们可能会建议具有一定编码经验的开发人员选择 Flutter 而不是 React Native，因为 Flutter 提供的 API 更接近您在 iOS 和 Android 中可以找到的 API。

此外，Flutter 背后的团队非常注重确保 Flutter 提供的开发体验能够与其他 SDK（包括 React Native）提供的开发体验相媲美。

## 2023 年 Flutter vs. React Native

React Native 于 2015 年问世，此后被许多公司使用。JavaScript 世界变化非常快，因此 React Native 也随着时间的推移而发展以包含新功能，这要归功于开源社区的贡献。

Flutter 是一项更新得多的技术，如果您来自 Android 或 iOS 世界，可能会感觉很陌生。Google 付出了很多努力让它变得非常容易学习，所以大多数熟悉 iOS 或 Android 开发的人应该能够很快上手。