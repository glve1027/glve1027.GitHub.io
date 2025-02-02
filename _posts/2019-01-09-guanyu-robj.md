---
layout:       post
title:        "关于R.Obj工具"
author:       "GH"
header-style: text
catalog:      true
tags:
    - Object-C
---

### 目的
1. 想通过R.Obj这个工具在编译的时候，去管理图片、字符串、主题的资源文件？？
2. 可能会存在多个静态库、或者动态库？？
3. 以及后期如何自定义？？

<!-- more -->
### R.obj 问题：
1. 如果图片不放在Assest中的话，会有问题：例如存在图片 `Test@2x.png、Test@3x.png, 最后编译成的模板会存在两张图片名称Test2x、Test3x`，如果统一将图片统一放入到Assest中，就不会有这个问题。❌
2. 如果图片不放在Assest中的话，它形成的模板的代码如下, 自动会带上图片的后缀，但是放入到Asstet中则正常。❌
```java
- (UIImage*)wifiConnectingShowBg { return [UIImage imageNamed:@"Wifi_connecting_show_bg.png"]; }
```
>  如果主项目中存在`R.obj`,那么存在于主目录中的任意多个`Bundle`，其中图片可能存在重复多个，但是生成的`R.obj`的模板文件是唯一的。但是本质上没有去重。❌  (这里可以通过编写脚本删除，在else的时候执行删除脚本)。
> 直接替换掉图片，可以同步。✔️ （每次编译的时候，都会每次生成一次模板）

### 静态库和R.obj 配合使用的问题：
#### 情况一：
1. 创建了一套.framework、.a的静态库。
2. 创建了一套.bundle的资源文件。（静态库没有直接调用bundle里面的资源）
3. 经过R.obj编译过后。
#### 情况一的结果：
* 外部可以直接调用静态库中的代码 ✔️
* 图片资源的名称，目前没法制定Bundle的前缀名称 ❌

#### 情况二：
1. 创建了一套.framework、.a的静态库, 直接通过代码的方式获取外部公用bundle的资源。
2. 经过R.obj编译过后。代码如下：
```java
icon.image = [UIImage imageNamed:@"外部bundle/通过R.obj生成的图片"];
```
#### 情况二的结果：
* 首先自动生成的R.obj会冲突，无不发编译。❌
* 在静态库中通过调用R.obj编译后的模板代码，是可以显示正常，并且外部可以通过静态库调起视图。✔️

#### 情况三：
1. 创建了一套.framework、.a的静态库。
2. 多套bundle资源。（这里可能会重复）
3. 经过R.obj编译过后。
#### 情况三的结果：
* 如果主项目中存在R.obj,那么存在于主目录中的任意多个Bundle，其中图片可能存在重复多个，但是生成的R.obj的模板文件是唯一的。但是本质上没有去重。❌ (这里可以通过编写脚本删除，在else的时候执行删除脚本)
