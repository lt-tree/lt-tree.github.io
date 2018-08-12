---
title: TreeHouse装修指南之菜单
date: 2018-07-17 22:43:00
tags: 想就做

---

本文章讲述了TreeHouse由无到有, 不断开枝散叶的过程。
（通俗点说，就是hexo的相关搭建及应用的各效果的实现）

菜单相关

<!-- more -->

<br/>

# 公益404
腾讯公益404页面，寻找丢失儿童，做一些力所能及的事情。
需要新建 404.html 页面，放到主题的 source 目录下：

```
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
  <link rel="stylesheet" type="text/css" href="https://qzone.qq.com/gy/404/style/404style.css">
</head>
<body>
  <script type="text/plain" src="http://www.qq.com/404/search_children.js"
          charset="utf-8" homePageUrl="/"
          homePageName="回到我的主页">
  </script>
  <script src="https://qzone.qq.com/gy/404/data.js" charset="utf-8"></script>
  <script src="https://qzone.qq.com/gy/404/page.js" charset="utf-8"></script>
</body>
</html>
```
<br/>

# 友链
修改文件： \themes\next\\\_config.yml

```
# Blog rolls
links_icon: link 					icon名字
links_title: Links 					标题名字
links_layout: block 				排版：竖排，一个链接一行
# links_layout: inline  			排版：横排，n个链接一行
links:
  #Title: http://example.com/		名称（可以是中文）：网址
```

<br/>

# 添加个人头像&头像旋转
## 添加
修改next主题配置文件 \_config.yml

```
avatar: image_url
```
image_url 可以是public文件夹下的相对路径，也可以是网络地址。

## 头像旋转
修改文件： \themes\next\source\css\\\_common\components\sidebar\sidebar-author.styl

```stylus
.site-author-image {
  display: block;
  margin: 0 auto;
  padding: $site-author-image-padding;
  max-width: $site-author-image-width;
  height: $site-author-image-height;
  border: $site-author-image-border-width solid $site-author-image-border-color;

  /* 设置圆形头像 */
  border-radius: 58px;
  -webkit-border-radius: 58px;
  -moz-border-radius: 58px;
  box-shadow: inset 0 -1px 0 #333sf;

  /* 鼠标经过头像旋转360度 */
  -webkit-transition: -webkit-transform 1.0s ease-out;
  -moz-transition: -moz-transform 1.0s ease-out;
  transition: transform 1.0s ease-out;
}

img:hover {
  /* 鼠标经过头像旋转360度 */
  -webkit-transform: rotateY(360deg);
  -moz-transform: rotateY(360deg);
  transform: rotateY(360deg);
}

/* Z 轴旋转动画 */
@-webkit-keyframes play {
  0% {
    -webkit-transform: rotateY(0deg);
  }
  100% {
    -webkit-transform: rotateY(-360deg);
  }
}
@-moz-keyframes play {
  0% {
    -moz-transform: rotateY(0deg);
  }
  100% {
    -moz-transform: rotateY(-360deg);
  }
}
@keyframes play {
  0% {
    transform: rotateY(0deg);
  }
  100% {
    transform: rotateY(-360deg);
  }
}

.site-author-name {
  margin: $site-author-name-margin;
  text-align: $site-author-name-align;
  color: $site-author-name-color;
  font-weight: $site-author-name-weight;
}

.site-description {
  margin-top: $site-description-margin-top;
  text-align: $site-description-align;
  font-size: $site-description-font-size;
  color: $site-description-color;
}
```

<br/>

# 社交小图标相关
修改文件： \themes\next\\\_config.yml

```
social:
  GitHub: github_address || github
  CSDN: csdn_address || copyright
  E-Mail: mailto:email_address || envelope
  微博: weibo_address || weibo

social_icons:
  enable: true

  GitHub: github
  CSDN: copyright
  E-Mail: envelope
  微博: weibo

  icons_only: false
  transition: false
```

social每行||后为图标名称，具体名称可以在 https://fontawesome.com/icons?d=gallery 中查到。


















