---
layout: post
title: 学习笔记 - Google官方文档 - 核心主题 - 架构组件
author: 贱子
date: 2021-01-27
tags: [Android]
---

学习笔记 - Google官方文档 - 核心主题 - 架构组件 <!--more-->

## 架构组件

### 视图绑定

1. 视图绑定功能可按模块启用。要在某个模块中启用视图绑定，请将 `viewBinding` 元素添加到其 `build.gradle` 文件中，如下例所示：

   ```groovy
   android {
   	...
   	viewBinding {
   		enabled = true
   	}
   }
   ```

2. 如果您希望在生成绑定类时忽略某个布局文件，请将 `tools:viewBindingIgnore="true"` 属性添加到相应布局文件的根视图中。

3. 为某个模块启用视图绑定功能后，系统会为该模块中包含的每个 XML 布局文件生成一个绑定类。每个绑定类均包含对根视图以及具有 ID 的所有视图的引用。系统会通过以下方式生成绑定类的名称：将 XML 文件的名称转换为驼峰式大小写，并在末尾添加“Binding”一词。

4. 在 Activity 中使用视图绑定。如需设置绑定类的实例以供 Activity 使用，请在 Activity 的 `onCreate()` 方法中执行以下步骤：

   1. 调用生成的绑定类中包含的静态 `inflate()` 方法。此操作会创建该绑定类的实例以供 Activity 使用。
   2. 通过调用 `getRoot()` 方法获取对根视图的引用。
   3. 将根视图传递到 `setContentView()`，使其成为屏幕上的活动视图。

5. 在 Fragment 中使用视图绑定。如需设置绑定类的实例以供 Fragment 使用，请在 Fragment 的 `onCreateView()` 方法中执行以下步骤：
   1. 调用生成的绑定类中包含的静态 `inflate()` 方法。此操作会创建该绑定类的实例以供 Fragment 使用。
   2. 通过调用 `getRoot()` 方法获取对根视图的引用。
   3. 从 `onCreateView()` 方法返回根视图，使其成为屏幕上的活动视图。
   4. **注意**：Fragment 的存在时间比其视图长。请务必在 Fragment 的 `onDestroyView()` 方法中清除对绑定类实例的所有引用。

6. 最好在项目中同时使用视图绑定和数据绑定。您可以在需要高级功能的布局中使用数据绑定，而在不需要高级功能的布局中使用视图绑定。

### 数据绑定库

`TODO` 未读

