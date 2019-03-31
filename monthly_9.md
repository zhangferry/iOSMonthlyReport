---
title: iOS开发月报#9|201903
date: 2019-03-31 21:30:57
tags: 月报
comments: true
---
这里记录过去一个月，我看到的值得分享的内容，包含但不限于iOS知识，每个月的最后一天发布。
欢迎推荐内容，可以前往[zhangferry/iOSMonthlyReport](https://github.com/zhangferry/iOSMonthlyReport)提交issue。
![image](http://upload-images.jianshu.io/upload_images/1059465-afa5d63933b5bf3c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Tips
### Spine + SpriteKit
项目中需要引入一些实物动画，每个动画之间有不同形态的切换，考虑过gif, mp4, AE + lottie, Spine + SpriteKit。

最后确定使用[Spine](http://zh.esotericsoftware.com/)做动画效果，用SpriteKit处理动画。Spin并没有官方支持SpirteKit的库，但有一个做的比较好的第三方库[maxgribov/Spine](https://github.com/maxgribov/Spine)，支持Swift4.1。

该库支持Bones, Slots, Skins等常用的动画要素，通过Spine导出的json文件和动画素材做出各种动画效果，是仅有的近期还在维护的支持SpriteKit的Spine运行时库。但它也存在一个问题，还不支持Mesh Animation(网格动画)。如果所需的动画效果不需要网格的话，非常推荐使用这个库。

而我们所需的动画效果又必须用到网格动画，思考再三考虑决定放弃使用这个库，使用SKTextureAtlas(纹理集) + 逐帧动画来实现特殊的动画效果。虽然输出的还是png序列，但是SpriteKit对纹理集有足够的优化，Xcode会在打包时把.atlas文件夹中的所有图片做成一张合图，然后生成一个plist文件描述每个小图片的位置信息，所以包的大小和渲染成本都会大大降低。

### 快速创建转场样式
说到自定义转场我们可能会直接想到`UIViewControllerAnimatedTransitioning`，结合这个类我们可以实现多种多样的订阅样式。但是使用这种方式做转场，我们需要引入很多代码。有一种简单的实现转场的方式是通过`CATransition`
> An object that provides an animated transition between a layer's states.

通过文档的介绍我们知道，这个类就是用来做转场的，只不过支持的样式有限，但如果正好满足你需要的话，推荐使用这种方式来实现转场。

我们来实现一个present的渐变效果：
```swift
//创建transition对象
let transition = CATransition()
transition.duration = 0.5
//动画样式
//type: .fade, .moveIn, .push, .reveal
transition.type = .fade
//动画出现方位
//subtype: .fromRight, .fromLeft, .fromTop, .fromBottem
//transition.subtype = .fromRight
transition.timingFunction = CAMediaTimingFunction(name: .easeIn)

self.view.window?.layer.add(transition, forKey: "present")
self.present(targetVc, animated: false, completion: completion)
```
如果是做push的渐变，我们只需要改变最后的控制动画的代码：
```swift
//父容器为UINavigationController
self.view.layer.add(transition, forKey: "push")
self.present(targetVc, animated: false, completion: completion)

//父容器为UITabbarController
self.tabBarController?.view.layer.add(transition, forKey: "push")
self.present(targetVc, animated: false, completion: completion)
```

### 什么是UserAgent
`User-Agent` 首部包含了一个特征字符串，用来让网络协议的对端来识别发起请求的用户代理软件的应用类型、操作系统、软件开发商以及版本号，然后前端的展示就可以根据这些信息进行针对性的优化。

我们打开Chrome浏览器，生成一个请求，然后用Charles抓包，可以看到对应的`User-Agent`
```js
user-agent  Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.28 Safari/537.36
```
通过UA我们可以得到以下信息：

| 信息项 | 内容 |
| --- | --- |
| 浏览器名称 | Chrome |
| 浏览器版本号 | 70.4.3729.28 |
| 渲染引擎 | WebKit 537.36 |
| 操作系统 | Mac OS 10.13.6 |

### Apple Configurator 2 出现 Unauthorized Error
注销账号，再次登录

### Command PhaseScriptExecution failed with a nonzero exit code
运行一个项目时遇到了这个bug提示，一直编译不过去，这其实是一个Xcode10引起的bug。
解决方案：
在Xcode菜单栏选择File -> Workspace Setting -> Build System 选择Legacy Build System 重新运行即可。
参考：[踩坑Xcode 10之New Build System](https://juejin.im/post/5ba35cc05188255c7c655a8c)
### 斐波那契函数
斐波那契数列（Fibonacci sequence），又称黄金分割数列、因数学家列昂纳多·斐波那契（Leonardoda Fibonacci）以兔子繁殖为例子而引入，故又称为“兔子数列”，指的是这样一个数列：1、1、2、3、5、8、13、21、34、……

这个是我们公司技术面试的必问题目，也是筛掉人数最多的一个问题。有一部分同学会使用数组尝试解决这个问题，但这会把问题复杂度升级，还有些可能根本没有思路。但其实这个问题不复杂的，用到递归可以很快的解决。斐波那契函数的数学表达是：
```
F(1)=1
F(2)=1
F(n)=F(n-1)+F(n-2)（n>=3，n∈N*）
```
用Swift实现就是：
```Swift
func fibonacci(n: Int) -> Int {
    if n == 1 || n == 2 {
        return 1
    } else {
        return fibonacci(n: n - 1) + fibonacci(n: n - 2)
    }
}
```

## 推荐阅读
### [SpriteKit Tutorial for Beginners](https://www.raywenderlich.com/71-spritekit-tutorial-for-beginners)
raywenderlich上介绍SpriteKit入门的一篇教程，通过这篇文章你可以实现一个忍者击杀怪物的小游戏，理解SpriteKit框架里常用的几种游戏元素。不得不说这个教程做的是真的棒👍

### [开发小知识](https://www.jianshu.com/p/5a4ba3c165b9)
该文章主要整理一些小知识点，主要涉及 iOS 以及计算基础相关知识点，某些知识点暂时只有标题，后续会持续更新。笔者最近一段时间面试过程中发现一些普遍现象，对于一些很不起眼的问题，很多开发者都只停留在知道、听说过的层面，但是一旦问 是什么 和 为什么 ，很多应试者回答的并不理想。
大家可以对着这篇文章查找自己的知识盲区。

### [Swift 5 终于来了，快来看看有什么更新！！](https://mp.weixin.qq.com/s/-fLVdoTz3lT5Kxnea0-Avg)
Xcode10.2 已经发布，是时候开始使用 Swift5 了，可以提前看下老司机周报总结的Swift5 更新内容，对适配工作做好准备。

### [苹果开了一场没有任何硬件的发布会](https://mp.weixin.qq.com/s/ggqK8kzU0efgTdPIZ5vbVw)
3 月 26 日凌晨 1 点，苹果在 Apple Park 新总部的乔布斯剧院召开了春季特别活动。

在活动现场，苹果发布了：
* 新闻服务 Apple News+
* 可以返现的 Apple Card
* 游戏服务 Apple Arcade
* 全新的 Apple TV App 服务
* Apple TV+ 原创视频服务

全部是软件和服务，没有新硬件的出现——这或许意味着，苹果正在寻找下一个十年的生长空间。
## 音视频
### [辞职环游中国的程序员小 K](https://talk.swift.gg/19)
大概每个人都有过这种冲动，辞掉工作出去旅行，想去哪就去哪，再也不用赶需求修 bug 通宵加班。不过对大多数人来说，也就止步于“想想”，并不会付诸行动。但是我身边有一位朋友，真的做到了这件事：辞职一年环游中国！

这是最近几期ggtalk对我触动最大的一期，同样是做iOS开发的，为什么人家那么优秀🙃
## Github
### [996.ICU](https://github.com/996icu/996.ICU)
工作996，生病ICU。这段时间的“明星项目”，旨在反抗国内互联网公司形成的每周工作6天，每天工作早9点到晚9点的不良加班风气。截止到3月30号，仅四天时间star已经超11万。

### [XVim2](https://github.com/XVimProject/XVim2)
XVim2是一个用于Xcode的Vim插件。如果你是一个Vim党，你可以直接在Xcode代码编辑界面使用Vim的各种特性。我是最近开始接触，也在慢慢适应Vim的远离鼠标工作模式。另附送一个安装流程：

1、关闭Xcode
2、钥匙串->证书助理->证书创建
    名称：XcodeSigner
    身份类型：自签名根证书
    证书类型：代码签名
3、重新签署Xcode
```bash
#需要等待一段时间
sudo codesign -f -s XcodeSigner /Applications/Xcode.app 
```
4、按照官方步骤安装XVim2

## 文摘
1、培养出在没人监督自己的时候也能高效工作的自我责任感非常重要。你也可以拔这称为是具有一种性格或者具有一种素质，它们都是同一个概念。如果缺乏对自己的责任感，你将永远依赖外部动机来驱使你努力工作。你容易折服于一根胡萝卜的诱惑，也容易屈从于一根大棒的威胁。
--《软技能：代码之外的生存指南》

2、最终，它成为我自己的知识体系中严重的短板。没有花时间去彻底掌握Lambda表达式的工作原理，结果浪费了大把的时间。最后当我下决心花时间去了解Lambda表达式的时候，我只花了几个小时阅读并实践，就领会了这一概念。
--《软技能：代码之外的生存指南》

3、回到从前，在我们刚开始一起生活的时候，我们就决定将我们收入的10%用于风险什一税--实际上我们把这部分收入捐给一家慈善机构，以帮助印度的孤儿。在我们第一次奉献什一税的第二周，我的妻子就得到了加薪，加薪的数额正好是我们当时奉献什一税的数额。我个人认为，我们的成功很大一部分就是因为这种对奉献的承诺，一直恪守到今天。
即使你不信仰任何宗教，我认为这一点也有某种符合逻辑的解释。我认为，你把钱看的越重，你就越难以在理财方面做出明智的、成功的投资选择。自愿把自己收入的固定数额奉献或者捐赠给慈善机构，可以改变你对金钱的看法。这一思想上的转变让你从金钱的所有者变成管理者。
--《软技能：代码之外的生存指南》