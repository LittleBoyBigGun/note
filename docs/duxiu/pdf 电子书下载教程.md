#duxiu 
`2023/3/14`

---
互联网下载出版书籍电子版， 应该遵循如下路径：

1. 在搜索引擎搜索， 一般能够搜索到一些别人分享的书籍
2. 在网盘搜索引擎搜索
3. 尝试从免费书源获取
4. 尝试在论坛让别人免费代寻
5. 在微信阅读， 文泉书局， 异步社区等电子书网址购买
6. 淘宝代寻

# 书源
---

目前有几个比较大的书源：

- `zlibrary` 拥有大量的英文书籍， 可以找到许多文字版的 PDF。
- `ylibrary` 可以找到大量的中文书籍，多为扫描版 ,书籍多数来自`读秀`泄漏的书籍。
- `异步社区` 拥有大量 IT 类书籍， 目前是收费
- `CADAL` 中美合建， 拥有几百万册书籍
- `国家哲学社会科学文献中心`， 社科类书籍居多
- `国立公文書館 / 内阁文库`, 日本人建立的收藏汉日古籍

另外还有一些其他书源， 本质上和 ylibrary 没多大区别， 在后面会有所提及。

## 读秀

超星扫描了四五百万种图书，制作成pdg格式电子书，以读秀、龙岩网络图书馆、深圳文献港等品牌向机构用户提供目录搜索、试读、电子全文（汇雅电子书）、包库、文献传递等服务，网上流传的中文扫描pdf电子书多半来源于此。

如果你不在读秀服务的机构，可以使用[全国图书馆参考咨询联盟 UCDRS](http://book.ucdrs.superlib.net/)，它是[读秀](https://www.duxiu.com/)的开放替代者，虽不提供电子书整本阅读，但其图书目录和搜索功能很有用。具体怎么用呢，要从代找PDF说起。

代找PDF的伙计们从哪里下载电子书？首先他们有“自建库”或“自建库”的入口，自建库就是读秀的镜像，只不过不是源自官方的完整镜像，而是多渠道陆陆续续积攒起来的，具体可以百度一下。可自建库的数据量高达几百T，里面有几百万个文件，如何定位到需要的文件呢？读秀或ucdrs上的每本图书有ssid和dxid两个编号，而自建库里的书就是用ssid命名的，他们就是通过这个编号查找电子档。dxid有什么用呢？因为读秀某时间点后隐藏了很多书的ssid，而没有隐藏dxid，所以如果有人在读秀隐藏ssid之前记录了dxid和ssid的映射表的话，你提供dxid，他也知道你要找的书的ssid是多少。

所以，本脚本就是在你访问ucdrs时显示某本书的ssid或dxid，如果你有机会接触到读秀的资源库，就可据此定位图书的电子文档。

小知识：

-   自建库有读秀2.0、3.0、4.0、5.0的版本区别，这个版本是读秀定义的，目前是5.0，从 [https://www.duxiu.com/bottom/version.html](https://www.duxiu.com/bottom/version.html) 可以看得到。2022年2月15日前后4.0升级为5.0，而最迟在2017年6月30日时，还是2.0 ([https://web.archive.org/web/20170630071746/https://www.duxiu.com/bottom/version.html](https://web.archive.org/web/20170630071746/https://www.duxiu.com/bottom/version.html))，3.0是哪年的，我也不知道。
-   pdg文件通常打包为zip文件，解压后可以很方便地转换为pdf，下载老马的Pdg2Pic即可，此工具免费 ([https://www.cnblogs.com/stronghorse/p/14594337.html](https://www.cnblogs.com/stronghorse/p/14594337.html))。

###  ss 号、 dx 号、ISBN 号

ss 号和 dx 号是读秀对书籍的编号 。ISBN 是国家分配的书籍发行编号， 也可以唯一确定书籍。此文也可以通过书籍名和作者来确定书籍， 但是不是很方便。另外一些嗅探， 下载， 目录获取等工具都是基于 ss 和 dx 号来制作的， 故而获取读秀源里边的书籍， 提供 ss 和 dx 最佳。

可以通过如下网址来获取图书的所有信息， 包括 ss , dx 号

- [互助联盟 (xueshu86.com)](https://u.xueshu86.com/)
- [全国图书馆参考咨询联盟 (superlib.net)](http://www.ucdrs.superlib.net/)
- [东莞图书馆文献检索 (superlib.net)](http://www.dglib.superlib.net/)

### 文献传递

图书馆没有的书籍， 可以通过文献传递的方式从其他图书馆获取。操作也不复杂， 发起文献传递请求， 把需要书籍的页码发送过去， 对方收到请求后通过邮箱发送书籍阅读链接。不过文献传递也有诸多限制， 一次不能请求整本书， 对文献传递的频率也做了限制， 返回的阅读链接也有时限。针对阅读链接的时限， 有工具可以直接下载到本地。

### pdg、uvz

超星使用 pdg 或 uvz 的形式来存储， 传输书籍， 有些资源还被加密。后面介绍一些工具， 可用于解密， 转成 pdf。

### 嗅探

图书源没有资源且图书馆拥有包文全库， 则可以使用嗅探工具来下载 pdg 文件。目前可用于嗅探的工具有：

- cxCandyEnt 也叫棒棒糖， 可以嗅探和下载
- httpanalyzerfull
- 百发百中报文嗅探器
- wsExplorer

使用方法请自行百度。

### pdg 转 pdf

使用 `pdg2pic`可以把 pdg 转换成 pdf， 同时挂载目录。

### uvz 转 pdf

uvz 文件本质上是个 zip 压缩包。解压后可以用 pdg2pdf 来转换， 有些加密的压缩包使用工具， 不过不保证一定可以解密成功。解密工具在压缩包 `解压合成工具2023.rar`中， 或者使用[zip2pdf](https://github.com/Davy-Zhou/zip2pdf/releases/tag/v0.4) 。也可使用在线的解密：https://pdg2pdf.online/convert

### 挂载目录

大部分书籍可以使用工具 `书签获取器` 来获取目录， 输入 ssid， 稍等片刻就可获取成功， 再使用 `pdgCntEditor` 来挂载目录， 先拖入 pdf， 把前面获取的目录粘贴进去， 并修改目录层级， 然后保存即可。少部分书， 可以通过豆瓣， [china-pub](http://www.china-pub.com/)淘宝京东书籍详情页等地方获取到目录， 不过挂载的时候要做层级和偏移的调整。如果上述方法都无法获取目录， 则可以通过 [QuickOutline ](https://github.com/ririv/QuickOutline/releases)来生成， 基于 ocr自动识别， 要求必须有目录页。

### 浏览器插件

一些浏览器插件可以帮助电子书的查找和下载:

- 可用于快速获取 ssid 等书籍信息。例如[文献互助小帮手](https://greasyfork.org/zh-CN/scripts/435569-%E6%96%87%E7%8C%AE%E4%BA%92%E5%8A%A9%E5%B0%8F%E5%B8%AE%E6%89%8B-%E8%AF%BB%E7%A7%80pdf%E4%B8%80%E9%94%AE%E4%B8%8B%E8%BD%BD-%E5%9B%BE%E4%B9%A6%E9%A6%86%E8%81%94%E7%9B%9F-%E8%AF%BB%E7%A7%80-%E8%B6%85%E6%98%9F-%E4%B8%AD%E7%BE%8E%E7%99%BE%E4%B8%87%E6%98%BE%E7%A4%BAssid%E7%AD%89%E7%B4%A2%E4%B9%A6%E5%8F%B7-%E5%90%84%E6%96%87%E7%8C%AE%E7%AB%99-%E5%9B%BE%E4%B9%A6%E7%94%B5%E5%95%86%E7%AB%99%E4%B8%8E%E8%B1%86%E7%93%A3%E7%9A%84%E4%BA%92%E8%AE%BF%E9%93%BE%E6%8E%A5-%E4%B8%80%E9%94%AE%E5%A4%8D%E5%88%B6%E5%85%83%E6%95%B0%E6%8D%AE)。
- 可用于传输文件， [秒传链接提取](https://greasyfork.org/zh-CN/scripts/424574-%E7%A7%92%E4%BC%A0%E9%93%BE%E6%8E%A5%E6%8F%90%E5%8F%96)

### 文件传输

免费中文书源的书籍一般存放于以下几个地方

1. 百度网盘， 使用秒传分享
2. telegram, 使用 tg 机器人来分发
3. 去中心化存储服务器

### 秒传

秒传链接是一种通过模拟网盘自带秒传功能实现的文件分享方式(非官方), 其优点是可以永久保证分享有效性(在官方不限制秒传功能前提下), 且秒传链接不包含任何账号信息. 使用秒传链接转存文件并没有任何加速下载的效果。

其插件官网为：[网页端 | 秒传链接提取脚本 - 文档&教程 (xtsat.github.io)](https://xtsat.github.io/rapid-upload-userscript-doc/document/Install/Web.html)

## zlibrary

zlibrary 是目前最大的外文书源， 22年底被美国政府以侵权为由取缔其在美国的服务器， 目前老网址已经不能访问使用。不过好在书籍本身还在， 网上可以下载到几百万册的 zlibrary 书籍， 也有许多网友重新假设网址提供服务,其中基于 ipfs 的`安娜的档案`最值得称赞。

### 安娜的档案

zlibrary 遭受重创后， 创始人继续寻找到一种去中心化， 稳定的书籍托管方式。ipfs 是一种不错的解决方案， 大量书籍被上传至 ipfs 网络， 姑且称之为 zlibrary 的 ipfs 化。[安娜的档案](https://zh.annas-archive.org/) 便是 ipfs 的成果。

安娜的档案提供书籍检索， 检索结果不仅有书籍的详细信息， 也包括 cid， 这是下载书籍的唯一凭证。

### ipfs

在 ipfs 网络上，每一个文件均有一个唯一的`cid`， 其为下载该文件的唯一凭证。ipfs 是一个基于 p2p 的去中心化网络，故而对于某个资源而言，  下载人数越多， 下载速度越快 ， 同时如果没有人下载和保持其存在， 该资源就会从 ipfs 上消失。

cid 是 ipfs 世界的钥匙， ipfs 网关则是 ipfs 世界的大门， 可以自己假设也可以使用别人提供的网关， 搜索引擎中就可以搜索出别人分享的网关。

[runningcheese/Awesome-Zlibrary (github.com)](https://github.com/runningcheese/Awesome-Zlibrary)

## CADAL

2000年12月，中美两国计算机专家共同发起了'中美百万册书数字图书馆合作计划'（China-US Million Book Digital Library Project），计划用五年时间由中美两国共建达百万册中英文图书的数字图书馆，以提供便捷的全球可访问的全文图书浏览服务。项目被中国教育部列为'十 五'期间'211工程'公共服务体系建设的重要组成部分，并与中国高等学校文献保障体系（CALIS）一起，构成中国高等教育数字化图书馆的框架。项 目名称定为'高等学校中英文图书数字化国际合作计划'（英文简称CADAL）。

CADAL项目一期（2001-2006）完成100万册图书数字化，提供便捷的全球可访问的图书浏览服务。CADAL项目二期（2007-2012）新增150万册图书数字化，构建了较完善的项目标准规范体系，初步建设分布全国的服务网络，CADAL项目从单纯的数据收集向技术与服务升级发展转变。2013年以后，CADAL项目进入运维保障期，继续在资源，服务，技术，对外交流合作等方面推进工作。百万册图书规模的数字资源建设主要服务于高校的教学和科研，同时兼顾到民族优秀文化遗产的保存与传承。

按语言封类，该库资源覆盖：

-   中文资源50万册：现代中文图书30万册，项目 参加单位的硕士、博士学位论文10万篇，民国（1911-1949）书籍10万册，古籍及其他珍贵的传统文化资源10万卷（件）以内；
-   英文资源50万册：美国大学图书馆核心馆藏中版权明晰的重要学术著作，其他无版权问题的英文资源，美方提供的10万册电子图书及学位论文、技术报告、会议录等原生数字化资料。

按形态分类，该库资源包括：

-   图书（古籍、民国图书、当代图书、外文图书）；
-   期刊（民国期刊、当代期刊）；
-   报纸；
-   学位论文（民国学位论文、当代学位论文）；
-   多媒体（音视频、图形图像）；
-   特藏资源（侨批、满铁、地方志、生活资料）等。

此项目早期声称拥有中美官方合作扫描百万册的中英文电子书，“中美百万”故而得名。中美百万CADAL第一期工程（2016年前）期间，有人用工具累积下载了60W+数字资源，这些资源目前可以在网上找到，文件名中有ssno，所以ssno可以辅助定位资源。这60W+资源主要是2005年以前浙大图书馆的库藏，基本是2000年以前的书（30W）和论文（30W），还有一些四川大学藏书。

## 国家哲学社会科学文献中心

“国家哲学社会科学文献中心”立足全国哲学社会科学领域，由国家投入和支持，开展哲学社会科学文献信息资源建设和服务。发布包含中文期刊、外文期刊、外文图书和古籍四大类的资源，收录哲学社会科学相关领域文献共计10,000,000余条，其中古籍一万三千多种，免费提供在线阅读、全文下载等服务；还收录有国内外哲学社会科学领域重要的政府机构、高等院校、学术机构以及数据库的链接，便于查阅、使用。

## 国立公文書館 / 内阁文库

内阁文库是日本一所收藏汉、 日文古籍的专门图书馆。藏书总量为54万册，其中日文书31万多册、汉籍18万多册、西文书4.5万多册。详见[内阁文库](https://baike.baidu.com/item/%E5%86%85%E9%98%81%E6%96%87%E5%BA%93/12576969)。

### ylibrary

Ylibrary 去中心化图书馆接口，整合了 zlib 和 superlib 的资源

# 易书
---

[易书 ](https://yibook.org/) 是免费书分享交流站点， 提供大量书籍搜索， 人员交流， 在下阅读， 以及大量免费书相关站点工具等。

# zhelper
---
[zhelper](https://zhelper.net/) 为开源图书搜索前端工具， 可以方便对接不同书源。假如你有大量书籍资源， 基于此可以方便地架设一个图书搜索站点。

一些接入 zhelper 的书源

- zlibrary [zlib.app](https://zlib.app/)
- 布克盘 [bookpan.net](https://bookpan.net/)
- [ylibrary.org](https://ylibrary.org/)

另外可以到搜索引擎搜索更多接入 zhelper 的书源。例如：[zhelper search 配置小工具 (yibook.org)](https://tool.yibook.org/) 

# book-searcher
---
[book-searcher](https://github.com/book-searcher-org/book-searcher) 由国人开发并开源的 zlibrary 书籍检索系统， 分前端和后端两部分。前端提供浏览器界面， 可以在 github 上获取， 后端则是通过 docker 镜像形式提供， 无法看到其源码。其书库书籍， 爬取自安娜的档案。

推荐一个开发者搭建的 demo 网站: [Book Searcher (knat.network)](https://zlib.knat.network/)

# 一些论坛
---

- [SSDOWN](https://ssdown.org/) 专门针对读秀源， 提供不错的教程
- [FreeMdict Forum](https://forum.freemdict.com/t/topic/2725) 这个论坛有相关读秀相关资源分享， 工具分享， 免费代寻等
- [FreeMbook Search](https://freembook.com/) 下载书籍网站， 不过下载的都是 pdg 格式
- [找书网 ](https://findbooks.eu.org/) 书籍资源众多， 使用秒传分享
- [My Book House](https://www.findbooks.info/) 又一个不错的找书网址， 也是通过秒传分享
- [互助联盟](https://u.xueshu86.com/) 有偿代寻
- [易书论坛](https://bbs.yibook.org/) ylibrary 交流论坛， 这里什么都有
- [TheFuture书籍搜索-免费电子书搜索引擎](https://bks.thefuture.top/)
- [Z-Library – the world’s largest e-book library. Your gateway to knowledge (zlibrary.tk)](https://zlibrary.tk/)
- [易书 电子书聚合搜索引擎 (yibook.org)](https://search.yibook.org/)
- [易书 ](https://yibook.org/) 什么都有
- [书伴](https://bookfere.com/tools#KindleGen) 提供许多工具， 主页面向电纸书用户
- [计算机类书籍](https://github.com/Jackpopc/CS-Books-Store) 一个公开的计算机类书籍的分享

