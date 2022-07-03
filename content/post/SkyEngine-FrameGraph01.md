---
title: FrameGraph(一)
date: 2022-07-03
authorbox: false
categories:
  - "Blog"
tags:
  - "Real Time Rendering"
  - "SkyEngine"
  - "FrameGraph"
---

## FrameGraph
概念来来源于[寒霜](https://www.bilibili.com/video/av10595011)，个人理解本质一种面向数据编程模式，利用单帧全局信息进行资源和 Task(Pass) 的调度优化。  
主要解决问题：
* 管线扩展性
* 资源自动配置管理
* 异步计算管线管理

其中扩展性需要以每帧构建的开销为代价。资源的配置管理分为两部分，一是内存的生命周期管理、内存复用，二是资源的动态 Barrier 处理。  
异步管线（个人理解还不太完善后续补充），后续分几篇介绍 SkyEngine FrameGraph。

### 扩展性问题
引擎的设计初衷是一个可定制化的专用引擎，因此希望做到模块化。对于引擎模块化来说，需要解决如何在各模块动态定制管线的问题。目前想法是依赖 project 的配置来实现，两种方法，一管线资产化，二管线脚本化，渲染核心无固定的管线结构。

### 资源管理问题
现代游戏动辄300+ pass，在 GPU 资源有限的情况下必须要尽可能复用内存，存在两种做法
* 对象池管理，复用 Image\Buffer 对象
  * 优点：相对简单、兼容性好
  * 缺点：格式、大小等限制
* 内存池管理，Image\Buffer 对象复用 Memory
  * 优点：限制小、复用率高
  * 缺点：依赖现代 API，相对复杂的内存管理

为实现内存池管理，依赖的如 Vulkan API 下 Buffer\Image 对象绑定 Memory 对象的操作，这边有一个假设前提，__节约显存带来的收益大于额外的构建开销或者额外的构建开销可忽略不计__。

额外的构建开销包括 ImageView\FrameBuffer 的构建，受限于本人对驱动的了解，这部分可能要留作后续研究、试验确定，个人浅显的理解 View\FrameBuffer 仅为描述资源\容器，构建开销并不应该很大。