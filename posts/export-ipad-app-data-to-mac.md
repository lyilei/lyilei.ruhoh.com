---
title: 导出iPad上App的数据
date: '2013-01-18'
description:
categories: 
  - iOS,
  - 移动开发
tags:
  - iOS,
  - 移动开发
---

近日，客户在使用我们半年前release的一个iOS App时发现了一个比较严重的bug。bug的症状是App在利用服务器端数据更新本地数据时产生了错误，使得数据之间的映射关系发生了混乱。虽然数据没有被丢失，但是由于映射关系错乱了，导致整个App亦不可再被正常使用。bug发生的几率被客户报告为50%。

在经过几轮简单的本地测试后，没有发现客户说的问题。于是，怀疑App Store上面的App不是最新的code，并从其上下载一个版本并进行测试。结果经过几轮测试后，重现了问题。因此就想把App里面的数据导入到Mac上，然后从数据入手看是否能够查找到问题的根源。Google之后，发现Xcode早就提供了这样的机制。

首先把装有该App的iPad连接到Xcode，然后进入Organization，参考下图的具体下载数据步骤。
![Download Data from Xcode]({{urls.media}}/download_data_from_xcode.png)

下载下来的数据格式是XCAPPDATA，通过Show Package Content可以看到程序的目录结构如下：
![Directory Structure of XCAPPDATA]({{urls.media}}/directory_structure_of_xcappdata.png)

这样我们就拿到了iPad里面我们App的真实数据，其实就是一个SQLite数据库，这里推荐使用<a href="http://mesasqlite.en.softonic.com/mac" target="_blank">MesaSQLite</a>打开和查询里面的数据。

—————华丽丽的分割线——————

导致上面bug的根本原因是由于一对全局的JavaScript变量在开始新的一轮与数据库同步之前没有被正确地reset，这个错误花费了整整两天的时间才找到。建议是尽量少的使用全局变量，如果一定要使用的话，最好将其作为系统中有且仅有的一个全局对象的属性。同时，在使用这些属性之前，如果仅仅是被用做标识的话，一定要仔细检查其上下文，如有必要就重置为默认值后再使用。

在刚刚开始的时候，HTML5 worker被当成了第一怀疑对象。从重新的状况来看，太像是worker在新的一轮更新中执行了两次。其实，workder只是被调用，并不是主动运行了两次。