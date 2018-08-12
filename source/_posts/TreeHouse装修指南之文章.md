---
title: TreeHouse装修指南之文章
date: 2018-07-17 22:43:00
tags: [hexo搭建, 想就做]

---

本文章讲述了TreeHouse由无到有, 不断开枝散叶的过程。
（通俗点说，就是hexo的相关搭建及应用的各效果的实现）

文章相关

<!-- more -->

<br/>

# 添加摘要图片, 在正文不显示
首先加入相关配置设定
在 

```
\themes\next\layout\_macro\post.swing
```

中加入配置代码:

```
{% if post.summary_img  %}
  <div class="out-img-topic">
    <img src={{ post.summary_img }} class="img-topic">
  </div>
{% endif %}
```

添加位置是:

```
{% if is_index %}
    在这里插入代码
    {% if post.description and theme.excerpt_description %}
      {{ post.description }}
      <!--noindex-->
      <div class="post-button text-center">
        <a class="btn" href="{{ url_for(post.path) }}">
          {{ __('post.read_more') }} &raquo;
        </a>
      </div>
      <!--/noindex-->
    {% elif post.excerpt  %}
      {{ post.excerpt }}
      <!--noindex-->
      <div class="post-button text-center">
        <a class="btn" href="{{ url_for(post.path) }}{% if theme.scroll_to_more %}#{{ __('post.more') }}{% endif %}" rel="contents">
          {{ __('post.read_more') }} &raquo;
        </a>
      </div>
      <!--/noindex-->
    {% elif theme.auto_excerpt.enable %}
      {% set content = post.content | striptags %}
      {{ content.substring(0, theme.auto_excerpt.length) }}
      {% if content.length > theme.auto_excerpt.length %}...{% endif %}
      <!--noindex-->
      <div class="post-button text-center">
        <a class="btn" href="{{ url_for(post.path) }}{% if theme.scroll_to_more %}#{{ __('post.more') }}{% endif %}" rel="contents">
          {{ __('post.read_more') }} &raquo;
        </a>
      </div>
      <!--/noindex-->
    {% else %}
      {% if post.type === 'picture' %}
        <a href="{{ url_for(post.path) }}">{{ post.content }}</a>
      {% else %}
        {{ post.content }}
      {% endif %}
    {% endif %}
  {% else %}
    {{ post.content }}
{% endif  %}
```

然后在文章那部分加字段及图片位置名称

```
summary_img: image_url
```

image_url 是public文件夹下的相对路径

<br/>

# 添加摘要图片, 在正文显示
直接在相应位置加入图片即可

```
<img src= image_url>
```

或者
```
![](image_url)
```
image_url 可以是public文件夹下的相对路径，也可以是网络地址。

<br/>

# 代码块相关设置

主题配置文件: 

```
\themes\next\_config.yml 
```

代码块高亮，共五种模式，效果展示可以看注释里面的网址

```
# Code Highlight theme
# Available value:
#    normal | night | night eighties | night blue | night bright
# https://github.com/chriskempson/tomorrow-theme
highlight_theme: night
```
要让代码各种颜色，就需要注意格式


\`\`\`语言
  代码
\`\`\`


具体语言用哪个可以看这个列表: https://prismjs.com/#languages-list
例如 Objective-C 要用 objectivec

<br/>

# 文章tag显示修改
在文章底部的tag，显示样子，默认是 #tag 改成 图片tag

修改主题模板文件：

```
\themes\next\layout\_macro\post.swig
将 <a href="{{ url_for(tag.path) }}" rel="tag">#
替换成 <a href="{{ url_for(tag.path) }}" rel="tag"><i class="fa fa-tag"></i>
```

<br/>

# 超链接样式
将文章内的超链接样式修改一下，默认显示一个颜色，鼠标移动到链接上显示另一种颜色。

修改主题模板文件：

```
themes\next\source\css\_common\components\post\post.styl
添加相应修改：

// 文章内链接文本样式
.post-body p a{               /*  修改文本内的内容  p是为了不影响首页 阅读全文的样式 */
  color: #0593d3;             /*  默认显示文本颜色    */
  border-bottom: 1px solid #0593d3;     /*  底部下划线 1像素 实线 颜色 */
  &:hover {                 /*  鼠标移动上去后显示的文本颜色  */
    color: #fc6423;       
    border-bottom: 1px solid #fc6423;
  }
}
```

<br/>

# 添加文章版权信息
将主题配置文件的copytright打开，并修改license相关

```
# Declare license on posts
post_copyright:
  enable: true
  license: CC BY-NC-SA 3.0 CN
  license_url: https://creativecommons.org/licenses/by-nc-sa/3.0/cn/
```

修改copytright的链接地址：
```
themes\next\layout\_macro\post-copyright.swig
```
将其中链接部分，修改一下：
```
<a href="http://www.lt-tree.com/{{ post.path | default(post.permalink) }}" title="{{ post.title }}">http://www.lt-tree.com/{{ post.path | default(post.permalink) }}</a>
```