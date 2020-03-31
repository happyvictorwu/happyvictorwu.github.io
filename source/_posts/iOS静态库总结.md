---
title: iOS静态库总结
date: 2020-03-31 10:44:08
categories:
- [iOS, 静态库]
tags:
- 编程
---

# iOS静态库使用总结



## 总体流程

1. 创建Cocoa Touch Static Library 实现对应的逻辑
2. 设置暴露的头文件
3. 模拟器和真机分别编译。 （分release和debug，可以在Edit Scheme中 Build Configuration中选择debug或者release,对外提供的framework尽量是relese版本）
4. 合并静态库 `lipo -create xx1.a x2.a -output xx3.a` （把静态库xx1和xx2，合并成一个 xx3静态库）



* 静态库是iOS中资源打包的方式  `.a`文件 （在Product文件夹中）
* 需要手动引入头文件使用
* 一般要设置Other Linker Flags 等



## PS

* 因为静态库`.a`文件是一个二进制文件，需要在Target-Build Phases-Copy Files中添加对应的头文件。编译之后，会在Product include文件夹中得到暴露的头文件，配合二进制的文件，使用者就可以使用静态库
* 每次编译的时候会在Product文件生成`.a`。真机和模拟器的架构不一样，需要分别编译然后合成成一个来方便调试（第四步）。但是在上线项目是不需要打包模拟器版本的，原因是减伤包的体积。



## 使用静态库流程

1. 在项目创建一个Group（文件夹），想添加普通文件一样把静态库`.a`和其对应的头文件添加入Group中
   * （右键点击Show in finder, 先把文件拖入finder中文件夹，回到Xcode中 Add Files to "<GroupName>" .）
2. 重新编译项目