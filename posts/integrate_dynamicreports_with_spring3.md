---
title: 集成DynamicReports到Spring3中
date: '2013-03-29'
description:
categories:
  - 企业开发
  - Spring
tags:
  - Spring
  - DynamicReports
  - JasperReport
---
####背景介绍####
需要在报表的具体列上增加控制权限，也就是说，不同权限的人在浏览同一个报表时会呈现出不同的列。这样的情形下，选择了基于 [JasperReports](http://community.jaspersoft.com/project/jasperreports-library) 的 [DynamicReports](http://www.dynamicreports.org/)，因其可以动态地通过 code 来设计报表，以下是其官方的自我介绍：
> DynamicReports is based on JasperReports. It allows to create dynamic report designs and it doesn’t need a visual report designer.  
不管是通过其 source code 的 Docs 还是其 [Forum](http://www.dynamicreports.org/forum/) 的维护，始终都都感觉这个 DynamicReports 是一个大侠开发出来并在做着维护。

####集成思路####
Spring3 MVC 中已经提供了对 JasperReports 的支持，因为最先想到的办法就是直接应该相关的 ViewResolver 和 JasperReports Views。通过其源代码发现这些类还是基于报表的 jrxml 设计文件来实现具体报表的加载，填充和 render，而和我们想要的动态加载报表并显示出来完全不一致了。于是决定自己来实现相应的 ViewResolver 和 Views.

我们最终的目的是将 DynamicReports 动态产生出的报表的内容在 Spring3 MVC 的框架结构下显示在页面上，不管报表的最终输出格式是 PDF, HTML，还是Excel。基于此，我们的实现思路就是将报表的输出内容放到 Response 里面（这里将 Response 看成是输出的 OutputStream），同时，在将内容放入之前针对不同的格式做一些相应的设置。这样我们的实现思路就明确了。

- 设计图
- 实现细节
- 集成/配置方法
