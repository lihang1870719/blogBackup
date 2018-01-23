---
title: three.js
date: 2016-11-15 23:37:19
tags: three.js javascript
---

## 编辑器： sublime text 有相关的插件：
https://github.com/blackjk3/threejs-sublime

## demo使用方式：
1、安装node
2、npm install -g http-server
3、当前目录下输入 http-server
4、浏览器中输入 http://127.0.0.1:8080/race.html
下载地址：email 给你们unionpay的邮箱

## 目前来说需要做的地方

- 项目框架搭建
- 整体布局 包括场景中各个模块的位置、光源的配置、相机的移动、材质的选择等
- 各个模块 包括不局限 建筑物、天空、车、树、灯、人、小湖
	- 建筑物：由于是不规则建筑物，建议通过网格对象来实现，也就是new THREE.Geometry
	- 天空：官网有相关的例子，直接拿过来用就可以了，主要的实现方式是将场景放置到一个立方体中
	- 车：需要将模型导入到threejs中 具体可以参考 http://www.hewebgl.com/article/getarticle/126
	- 灯：灯就是一个圆柱体，上面和下面颜色不一样
	- 人：人也需要去找一些模型，最后导入到threejs中
	- 湖：官网也有相关例子，但是考虑到性能上的问题，可能不会做的很复杂

## 技术难点

- 动画的优化与移动相机
- 定制着色器
- 光线跟踪渲染

好吧，貌似不知道怎么写。。。

## 技术栈

- threejs
- promise 异步流程控制
- html5相关 例如 svg canvas webwork等

## 相关链接
threejs 官网
https://threejs.org/examplesthreejs 
threejs中文文档
http://techbrood.com/threejs/docs/
threejs 相关书籍
https://read.douban.com/reader/ebook/7412854/
http://www.hewebgl.com/article/getarticle/27
http://www.ituring.com.cn/minibook/792
http://www.jianshu.com/p/92771817c73f
https://zhuanlan.zhihu.com/p/20796329?refer=sayfe
视频教程
http://www.jikexueyuan.com/course/threejs/



