---
title: 浏览器性能优化(概览)
date: 2022-02-13 10:52:05
categories: 
- 高级前端笔记
tags: 
- 性能优化
---


##### 性能优化
性能优化不是一下子就能做好的，他是一个迭代优化的过程
根据性能指标测量
- 浏览器自有的调试工具 DevTools
- 灯塔：网站整体质量评估，并给出优化建议

性能优化
- 从发出请求到收到响应的优化，比如DNS查询、HTTP长连接、HTTP 2、HTTP压缩、HTTP缓存等。
- 关键渲染路径优化，比如是否存在不必要的重绘和回流。
- 加载过程的优化，比如延迟加载，是否有不需要在首屏展示的非关键信息，占用了页面加载的时间。
- 资源优化，比如图片、视频等不同的格式类型会有不同的使用场景，在使用的过程中是召恰当。
- 构建优化，比如压缩合并、基于webpack 构建优化方案等。
- 等。。

##### 性能指标
https://web.dev
在讨论性能的时候，精确的、可量化的指标很重要
**google提出的Rail：**
Response：<100ms，如果没有结果也要给loding
Animation： 一帧大概10ms左右
ldle：执行时间尽量小于50ms，超过50ms的成为长任务
Load：首次加载应<5s，后续加载建议在2s内完成

6种优化指标
- First Contentful Paint (FCP)首次绘制，应小于2s
- Largest Contentful Paint (LCP)最大内容绘制，要考虑img video元素的封面图 等各大图片元素  应在2.5s以内
- First Input Delay (FID)首次输入延迟，常见原因是浏览器正忙于解析和执行应用程序加载的大量计算的JavaScript。很多html元素需要等待主线程结束才能开始，应在100ms内
- Time to lnteractive (TTI)网页到达完全可交互状态的时间点（是在最后一个长任务完成的时间，并在随后的5s内网络和主线是空闲的。3.8s内较好
- Total Block Time (TBT) FCP和TTI的总时间 阻塞时间 300ms内
- Cumulative Layout Shift (CLS)累计布局偏移0.1ms之内表现较好

##### web vitals
goole制定的性能标准 LCP FID CLS
测量 Web Vitals
性能测试工具，比如Lighthouse
·使用web-vitals 库
使用浏览器插件Web Vitals(应用商店）

性能测试概述
性能检测作为性能优化过程中的一环，它的目的通常是给后续优化工作提供指导方向、参考基线及前后对比的依据。性能检测并不是一次性执行结束后就完成的工作，它会在检测、记录和改进的迭代过程中不断重复，来协助网站的性能优化不断接近期望的效果。

Lighthouse：是一个由Google开发并开源的 Web性能测试工具，用于改进网络应用的质量。您可以将其作为一个Chrome扩展程序运行，或从命令行运行。您为Lighthouse提供一个您要审查的网址，它将针对此页面运行一连串的测试，然后生成一个有关页面性能的报告。
webPageTest：是个网站
DevTools：shift+esc任务管理器  network

##### 前端页面的生命周期
建立http请求：DNS通道和通信链路的建立
DNS：先找缓存 拿到ip
网络模型：应用 传输 网络 数据链路层
tcp链接：三次握手 四次挥手
反向代理服务器


#### 请求和响应优化介绍
目的:更快的内容到达时间。
核心思路:
1.更好的连接传输效率
2.更少的请求数量
3.更小的资源大小
4.合适的缓存策略

最佳实践:
1.减少DNS查找:每次主机名的解析都需要一次网络往返，从而增加了请求的延迟时间，同时还会阻塞后续的请求。
2．重用TCР 连接:尽可能的使用持久连接，以消除因TCP握手和慢启动导致的延迟。
3.减少HTTP重定向:HTTP冲定向需要额外的DNS查询、TCP握手等非常耗时，最佳的重定向次数为0。4．压缩传输的资源:比如Gzip、图片压缩。
5．使用缓存:比如HTTP缓存、CDN缓存、Service Worker 缓存。
6.使用CDN(内容分发网络)︰把数据放在离用户地理位置更近的地方，可以明显减少每次TCP连接的网络延迟，增大吞吐星,7．删除没有必要请求的资源。
8．在客户端缓存资源:缓存必要的应用资源，避免每次都重复请求相同的内容，例如多图片下载可以考虑使用缓存。
9.内容在传输前先压缩:传输数据之前应该先压缩应用资源，把要传输的字节减少到最小，在压缩的时候确保对每种不同的资源采用最好的压缩手段。10．消除不必要的请求开销:减少请求的HTTP首部数据(比如HTTP COokie)
11.并行处理请求和响应:请求和响应的排队都会导致延迟，可以尝试并行的处理请求和响应(利用多个HTP11连接实现并行下载，在可能的情况下使用HTP管道计数)，12．针对协议版本采取优化措施。升级到HTTP2.0。
拉勾教
13．根据需要采用服务端渲染方式。这种方式可以解决SPA应用首屏渲染慢的问题。
14．采用预渲染的方式快速加载静态页面。页面渲染的极致性能，比较适合静态页面。



#### 写在最后

原创博文, 如有错误, 敬请指导!