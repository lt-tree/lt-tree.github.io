---
title: TreeHouse装修指南之工具
date: 2018-07-17 22:43:00
tags: [hexo搭建, 想就做]

---

本文章讲述了TreeHouse由无到有, 不断开枝散叶的过程。
（通俗点说，就是hexo的相关搭建及应用的各效果的实现）

工具相关

<!-- more -->

<br/>

# 添加阅读统计功能
用LeanCloud加入阅读统计功能

1. 注册账号
[LeanCloud官网](https://leancloud.cn/ "点击前往注册")
2. 创建应用
点击应用，创建应用，输入应用名称（可修改），不勾选schema
3. 创建统计类
点击创建Class，保存博客的相关访问数据，Class名称为Counter（必须），设置不限制数据条目的ACL权限
4. 获取App ID与App Key
点击最左侧的设置，下属的应用Key，可以看到App ID及App Key
5. 配置App ID与App Key
打开Next主题的_config.yml文件，找到leancloud_visitors，配置相关字段，不要忘了enable要为true哟。
6. 对Web安全的设置
点击最左侧的设置，下属的安全中心，在Web安全域名处，设置可以更新数据的自己博客域名。

然后就可以在应用中的Counter类里看到相关阅读数据了，并且文章下面也有访问数量记录了。

<br/>

# 动态背景
我的next版本是5.1.4, 已经集成了这个功能，只需要打开开关即可（在next配置文件）

```
  # Canvas-nest
  canvas_nest: false  改成true
```

如果要修改相关配置（线条颜色，粗细，数量等等），可以在下面文件修改。

```
\themes\next\source\lib\canvas-nest\canvas-nest.min.js
```

背景的几何线条是采用的nest效果 
一个基于html5 canvas绘制的网页背景效果
来自github的开源项目 https://github.com/hustcc/canvas-nest.js

<br/>

# 设置网站的图标
在next主题的图片文件夹下，将相应图片替换即可。
```
\themes\next\source\images
```
相关图片配置如下：（注意格式和大小）
```
[下面代码 位于next的配置文件内]
favicon:
  small: /images/favicon-16x16-next.png
  medium: /images/favicon-32x32-next.png
  apple_touch_icon: /images/apple-touch-icon-next.png
  safari_pinned_tab: /images/logo.svg
```
<br/>

# 实现统计功能
增加字数统计及阅读时长

修改next主题配置文件：
```    
\themes\next\_config.yml 
```
修改下面的配置
```
# Post wordcount display settings
# Dependencies: https://github.com/willin/hexo-wordcount
post_wordcount:
  item_text: true
  wordcount: true     /*  字数统计      */
  min2read: true    /*  阅读时长统计    */
  totalcount: false   /*  总字数统计   */
  separated_meta: true
```
如果没有，就自己安装，在根目录下输入：
```    
npm install hexo-wordcount --save
```
然后再将上面配置添加到主题文件内。

到这里，显示的字数统计和阅读时间没有单位，需要修改文件：
```    
\themes\next\layout\_macro\post.swing
```
修改：
```
<span title="{{ __('post.wordcount') }}"> {{ wordcount(post.content) }} 字 </span>
<span title="{{ __('post.min2read') }}"> {{ min2read(post.content) }} 分钟 </span>
```