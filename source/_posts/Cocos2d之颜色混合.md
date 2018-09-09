---
title: Cocos2d之颜色混合
date: 2018-09-09 21:55:00
tags: [跟着学]

---

Cocos2d之颜色混合
BlendFunc、Sprite、ImageView

<!-- more -->

<br/>

# 颜色混合
## 概念
- 混合：指两种颜色的叠加方式
- 颜色：包括RGBA（红、绿、蓝、透明度）信息
- 源颜色：后加入点的颜色
- 目标颜色：先加入点的颜色
- 源因子：后加入点的颜色乘以的系数
- 目标因子：先加入点的颜色乘以的系数。

每个图片都可以看作是点的集合，此处只关注点的颜色信息。两张图片重合在一起时，就是部分点重合在一起，OpenGL在绘制时，会根据各自混合因子计算（源颜色*源因子 + 目标颜色*目标因子），最终得出表现颜色。

<br/>

## 计算公式
PS: 颜色分量最大值为1.0, 计算时不会按照超过1.0的值计算。
- 源颜色 (Rs, Gs, Bs, As)
- 目标颜色 (Rd, Gd, Bd, Ad)
- 源因子 (Sr, Sg, Sb, Sa)
- 目标因子 (Dr, Dg, Db, Da)

表达式: (Rs*Sr + Rd*Dr, Gs*Sg + Gd*Dg, Bs*Sb + Bd*Db, As*Sa + Ad*Da)

<br/>

## 混合因子


| 	混合方式 				|	因子值 						|	解释		|
| 	--- 					| 	--- 						|	---		| 
|	GL_ZERO					|	(0, 0, 0, 0)				|	该颜色不参与颜色混合（因为系数为0，乘多少都是0）	|
|	GL_ONE					|	(1, 1, 1, 1)				|	该颜色完整参与颜色混合（1是1，2是2，不打折扣） 	|
|	GL_SRC_COLOR			|	(Rs, Gs, Bs, As)			|	使用源颜色的各项数值做系数 	|
|	GL_DST_COLOR			|	(Rd, Gd, Bd, Ad)			|	使用目标颜色的各项数值做系数 	|
|	GL_ONE_MINUS_SRC_COLOR	|	(1-Rs, 1-Gs, 1-Bs, 1-As)	|	满值(1.0)减去源颜色的各项数值做系数 	|
|	GL_ONE_MINUS_DST_COLOR	|	(1-Rd, 1-Gd, 1-Bd, 1-Ad)	|	满值(1.0)减去目标颜色的各项数值做系数 	|
|	GL_SRC_ALPHA			|	(As, As, As, As)			|	使用源颜色的透明度做颜色的各项系数 		|
|	GL_DST_ALPHA			|	(Ad, Ad, Ad, Ad)			|	使用目标颜色的透明度做颜色的各项系数 	|
|	GL_ONE_MINUS_SRC_ALPHA	|	(1-As, 1-As, 1-As, 1-As)	|	满值(1.0)减去源颜色的透明度做颜色的各项系数 	|
|	GL_ONE_MINUS_DST_ALPHA	|	(1-Ad, 1-Ad, 1-Ad, 1-Ad)	|	满值(1.0)减去目标颜色的透明度做颜色的各项系数 	|



<br/>
<br/>

# Cocos2d相关基础
## BlendFunc
定义在 ccTypes.h 中，是一个存储源因子和目标因子的结构体。

内部定义了一些常用混合因子
```C++
/** Blending disabled. Uses {GL_ONE, GL_ZERO} */
static const BlendFunc DISABLE;
/** Blending enabled for textures with Alpha premultiplied. Uses {GL_ONE, GL_ONE_MINUS_SRC_ALPHA} */
static const BlendFunc ALPHA_PREMULTIPLIED;
/** Blending enabled for textures with Alpha NON premultiplied. Uses {GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA} */
static const BlendFunc ALPHA_NON_PREMULTIPLIED;
/** Enables Additive blending. Uses {GL_SRC_ALPHA, GL_ONE} */
static const BlendFunc ADDITIVE;
```

## BlendProtocol & TextureProtocol
均定义在协议基类 CCProtocols.h 中，
BlendProtocol类是用来设置/获取当前使用的BlendFunc（混合因子）的。
TextureProtocol继承了BlendProtocol类，同时提供了对当前纹理的操作支持。


<br/>
<br/>

# Sprite & ImageView
在Cocos2d中主要是会对图片进行颜色混合的设置，图片类主要就是Sprite和ImageView。

## Sprite
因为Sprite继承了TextureProtocol类，所以相应继承了对BlendFunc的操作。
直接使用 setBlendFunc/getBlendFunc 即可。

<br/>

## ImageView
虽然ImageView没有继承相关类，但是所有继承Widget的类都有获取绘制节点的方法 getVirtualRenderer。
ImageView重写了这个方法，会返回一个 Scale9Sprite，它是继承Sprite的，然后可以用对Sprite方法了。

<br/>
<br/>