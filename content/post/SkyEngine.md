---
title: SkyEngine 介绍
date: 2022-06-28
authorbox: false
categories:
  - "Blog"
tags:
  - "Real Time Rendering"
  - "SkyEngine"
menu:
  main:
    name: Blog
    weight: 4
---

# SkyEngine

## 初衷
SkyEngine 是业余时间写的一款渲染引擎，初衷是为了学习、验证等目的。本系列也是主要介绍引擎开发过程中的一部分思路和想法。  
主要思路：
* 逻辑层采用 ECS + JobSystem 设计思路
* 渲染器采用 FrameGraph + 组件化的整体思路。

## 关于Editor
引用 QT5 的 Native Editor，逐步开发中。

## 关于后端
引擎采用单 vulkan 后端的设计，主要考虑以下几个原因：
* Vulkan 目前跨平台（包括 MacOS\IOS 平台受益于 MoltenVK）有较好的兼容性。
* 避免 RHI 设计时对某一个 API 妥协。
* 【懒】。


## 功能

- [ ] framework
  - [x] 反射
  - [ ] 序列号\反序列化
  - [ ] AssetManager
  - [x] JobSystem
  - [x] Script
    - [x] Lua
    - [ ] JavaScript
    - 
  - [x] Launcher \ Application \ NativeWindow
    - [x] Windows
    - [x] MacOS
- [ ] engine
  - [x] Dynamic Modules
  - [ ] World \ Level
  - [ ] ECS
- [ ] render
    - [x] FrameGraph
    - [ ] 基础渲染
- [x] driver
    - [x] vulkan
      - [x] Windows
      - [x] MacOS
- [ ] Tools
    - [x] Editor
    - [ ] AssetBuilder
      - [ ] FBX / GLTF / ...
      - [ ] Shader

## 三方库
* assimp
* glm
* crc32c
* rapidjson
* taskflow
* stb-image
* imgui
* SFMT
* SPIRV_Cross
* SPIRV_Headers
* SPIRV_Tools
* glslang
* shaderc