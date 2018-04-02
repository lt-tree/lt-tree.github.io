---
title: cocos2d-x 接入 GameCenter 排行榜与成就
date: 2017-04-29 12:13:11
tags: [cocos2d, 想就做]
---

cocos2d-x 接入 GameCenter 排行榜与成就
[mac - XCode 8.3 - cocos2d-x lua]


<!-- more -->
### 前言

要求接入GameCenter的排行榜和成就。

GameCenter 是苹果推出的一个社交平台，
它主要提供了以下几个功能：
- 排行榜
- 成就
- 挑战
而且，苹果同时提供了GameKit框架来让GameCenter更易集成。


可能是因为GameCenter的热度过去了吧，相关的东西都比较早期。
整理总结了一下，希望对他人有所帮助。

我的环境：

mac - XCode 8.3 - cocos2d-x lua

<br/>
<br/>
<br/>

### 1. 公共的处理
<br/>

##### 1.1 配置 iTunes Connect 排行榜与成就的位置。
登录iTunes Connect, 找到要处理的APP。
选择 功能->GameCenter
可以看到三个大项：
- 移动群组
- 排行榜
- 成就

<br/>

##### 1.2 添加GameKit框架
打开项目工程，将 Capabilities 的 GameCenter 打开。
这样，XCode就会将GameKit框架加到我们的工程中。

<br/>

##### 1.3 登录GameCenter
登录GameCenter:  【这个步骤在我们加载完游戏时进行即可】


        -(void) authenticateLocalPlayer {
            // 获取本地用户
            GKLocalPlayer* localPlayer = [GKLocalPlayer localPlayer];
        
            // 认证登录
            localPlayer.authenticateHandler = ^(UIViewController *viewController, NSError *error) {
                [self setLastError:error];
        
                if (localPlayer.authenticated) {                        // 本地用户已经登录
                    _gameCenterFeaturesEnabled = YES;                   // 此变量是判断是否已经登录上GameCenter
                } else if(viewController) {                             // 没有用户，弹出登录界面
                    [self presentViewController:viewController];        
                } else {                                                // 没有用户，并且没有登录界面
                    _gameCenterFeaturesEnabled = NO;
                }
            };
        }


<br/>

##### 1.4 关于lua调用object-c
因为我的环境是 cocos2d-x lua，所以，用通过lua来调用object-c。
cocos2d-x其实已经有相关的调用结构 —— LuaObjcBridge, 可以直接用 callStaticMethod来调用：


        LuaObjcBridge.callStaticMethod(methodName className,args)


<br/>

##### 1.5 GameKit辅助处理类
GameKitHelper.h:



        #import <GameKit/GameKit.h>
        #import "cocos2d.h"
        
        // 方便lua调用
        #include "CCLuaEngine.h"
        #include "CCLuaBridge.h"
        
        @interface GameKitHelper : NSObject
        // 处理错误
        @property (nonatomic, readonly) NSError* lastError;
        
        // 初始化
        + (id) sharedGameKitHelper;
        
        // Player authentication, info
        -(void) authenticateLocalPlayer;
        @end



GameKitHelper.mm

        #import "GameKitHelper.h"
        
        @interface GameKitHelper ()
                <GKGameCenterControllerDelegate> {
            BOOL _gameCenterFeaturesEnabled;
        }
        @end
        
        @implementation GameKitHelper
        
        #pragma mark Singleton stuff
        
        +(id) sharedGameKitHelper {
            static GameKitHelper *sharedGameKitHelper;
            static dispatch_once_t onceToken;
            dispatch_once(&onceToken, ^{
                sharedGameKitHelper =
                        [[GameKitHelper alloc] init];
            });
            return sharedGameKitHelper;
        }
        
        #pragma mark Player Authentication
        
        -(void) authenticateLocalPlayer {
            GKLocalPlayer* localPlayer = [GKLocalPlayer localPlayer];
        
            localPlayer.authenticateHandler = ^(UIViewController *viewController, NSError *error) {
                [self setLastError:error];
                if (localPlayer.authenticated) {
                    _gameCenterFeaturesEnabled = YES;
                } else if(viewController) {
                    [self presentViewController:viewController];
                } else {
                    _gameCenterFeaturesEnabled = NO;
                }
            };
        }
        
        #pragma mark Property setters
        
        -(void) setLastError:(NSError*)error {
            _lastError = [error copy];
            if (_lastError) {
                NSLog(@"GameCenter -- setLastError -- ERROR: %@", [[_lastError userInfo] 
                  description]);
            }
        }
        
        #pragma mark UIViewController stuff
        
        -(UIViewController*) getRootViewController {
            return [UIApplication 
              sharedApplication].keyWindow.rootViewController;
        }
        
        -(void)presentViewController:(UIViewController*)vc {
            UIViewController* rootVC = [self getRootViewController];
            [rootVC presentViewController:vc animated:YES 
              completion:nil];
        }
        
        @end



<br/>

##### 1.6 登录GameCenter时机
由你决定，可以放在 AppDelegate 中 applicationDidFinishLaunching时。

<br/>
<br/>
<br/>

### 2. 关于排行榜
<br/>

#### 2.1 配置 iTunes Connect

在iTunes Connect 找到 排行榜。

简单说一下流程吧：
配置排行榜的结构，然后我们在游戏中将数据上传到这个结构，最后显示到GameCenter中。

排行榜分为 单个排行榜 与 组合排行榜。(顾名思义，区别就不需要解释了吧？)
里面需要配置的相应属性，可参考本文末尾的关于。

要注意两点：
1. 排行榜ID，因为只有它是在创建后无法更改的。（而且，要记住这个ID，因为后面程序要用到）
2. 排行榜只要发布了（经过审批发布），就无法删除了。

接下来就看属性去配置它吧。

<br/>
<br/>

#### 2.2 XCode工程配置

模拟这个流程：登录GameCenter -> 提交排行榜数据 ( -> 如果需要，弹出GameCenter排行榜)


提交排行榜数据:


        -(void) submitScore:(int64_t)score category:(NSString*)category {       // 这里两个参数 score是数据， category是ID，就是我们创建排行榜以后，不可更改的那个ID。
            // 检查是否在登录状态
            if (!_gameCenterFeaturesEnabled)    {
                NSLog(@"GameCenter -- submitScore -- Player not authenticated");
                return;
            }
        
            // 创建一个分数对象
            GKScore* gkScore = [[GKScore alloc] initWithCategory:category];
        
            // 设置分数对象的值
            gkScore.value = score;
        
            // 向GameCenter提交数据
            [gkScore reportScoreWithCompletionHandler: ^(NSError* error)    {
                [self setLastError:error];
            }];
        }



<br/>
<br/>

#### 2.3 实践使用

在公用部分，已经添加了GameCenter的登录验证相关的东西了。

* 将排行榜数据提交的函数

GameKitHelper.h


        -(void) submitScore:(int64_t)score category:(NSString*)category;


GameKitHelper.mm


        -(void) submitScore:(int64_t)score category:(NSString*)category {
        
            if (!_gameCenterFeaturesEnabled)    {
                NSLog(@"GameCenter -- submitScore -- Player not authenticated");
                return;
            }
        
            GKScore* gkScore = [[GKScore alloc] initWithCategory:category];
        
            gkScore.value = score;
        
            [gkScore reportScoreWithCompletionHandler: ^(NSError* error)    {
                [self setLastError:error];
            }];
        }


* 给lua调用的函数

GameKitHelper.h


        +(void) getScore:(NSDictionary *)dict;


GameKitHelper.mm


        +(void) getScore:(NSDictionary *)dict {
            NSString* rID = [dict objectForKey:@"id"];
            int score = [[dict objectForKey:@"score"] intValue];
            
            [[GameKitHelper sharedGameKitHelper] submitScore:(int64_t)score category:rID];
        }



* lua调用


        LuaObjcBridge.callStaticMethod("GameKitHelper", "getScore", {id = 排行榜的ID, score = 分数值})



<br/>
<br/>
<br/>

### 3. 关于成就

<br/>

#### 3.1 配置 iTunes Connect

还是老位置，之前看的排行榜，这次点成就。
还是老样子，成就的ID，创建后不可修改，成就发布后不可删除。
但是，成就还要多一点，就是每个游戏总共有2000点成就分（为什么是2000？你问老乔去。。），你可以给每个成就分配一些成就分。
成就还有是否隐藏的选项，但是它的隐藏并不是看不到这个成就了，而是有一个成就名称为隐藏的成就显示在列表中。
还有，它的进度是一个顺时针扇形来表示，就是你的图标刚开始是被遮掩的，随着进度的增加，会逐渐显现出图标，方向是顺时针扇形。

其他相关参数，可参考本文末尾的关于。


<br/>
<br/>

#### 3.2 XCode工程配置

流程与排行榜的一样，但是这次提交的不是分数了，而是成就完成的百分比。


        // 提交成就数据
        -(void) submitAchievment:(NSString *)identifier percent:(double) percentComplete {          // identifier 成就ID, percentComplete: 成就完成百分比
            // 判断登录认证
            if (!_gameCenterFeaturesEnabled)    {
                NSLog(@"GameCenter -- submitAchievment -- Player not authenticated");
                return;
            }
        
            // 创建成就结构，注入成就ID
            GKAchievement *achievement = [[GKAchievement alloc] initWithIdentifier:identifier];  
            
            // 设置成就百分比
            [achievement setPercentComplete:percentComplete];  
            
            // 提交成就数据
            [achievement reportAchievementWithCompletionHandler:^(NSError *error) {  
                if(error != nil){  
                    NSLog(@"GameCenter -- submitAchievment --  error:%@", [error localizedDescription]);  
                }else{  
                    NSLog(@"GameCenter -- submitAchievment --  提交成就成功");  
                }  
            }];  
        } 



<br/>
<br/>

#### 3.3 实践使用

同排行榜一样。


* 将成就数据提交的函数

GameKitHelper.h


        - (void)submitAchievment:(NSString *)identifier percent:(double)percentComplete;


GameKitHelper.mm


        -(void) submitAchievment:(NSString *)identifier percent:(double) percentComplete {
            if (!_gameCenterFeaturesEnabled)    {
                NSLog(@"GameCenter -- submitAchievment -- Player not authenticated");
                return;
            }
        
            GKAchievement *achievement = [[GKAchievement alloc] initWithIdentifier:identifier];  
              
            [achievement setPercentComplete:percentComplete];  
              
            [achievement reportAchievementWithCompletionHandler:^(NSError *error) {  
                if(error != nil){  
                    NSLog(@"GameCenter -- submitAchievment --  error:%@", [error localizedDescription]);  
                }else{  
                    NSLog(@"GameCenter -- submitAchievment --  提交成就成功");  
                }  
            }];  
        } 


* 给lua调用的函数

GameKitHelper.h


        +(void) getAchievement:(NSDictionary *)dict;


GameKitHelper.mm


        +(void) getAchievement:(NSDictionary *)dict {
            NSString* aID = [dict objectForKey:@"id"];
            double percent = [[dict objectForKey:@"percent"] doubleValue];
            
            [[GameKitHelper sharedGameKitHelper] submitAchievment:(NSString *)aID percent:percent];
        }



* lua调用

        
        LuaObjcBridge.callStaticMethod("GameKitHelper", "getAchievement", {id = 成就ID, percent = 成就百分比})



<br/>
<br/>
<br/>

### 4. 最后
GameCenter还是挺好的一个东西。
它还有一个好友挑战功能，但这个主要适合之前 Flappy Bird，别踩白块 那些游戏来弄。
或许，这也是这个平台没落了的原因吧。


<br/>
<br/>
<br/>

### 关于

- [关于 LuaObjcBridge](http://www.cocos2d-x.org/reference/native-cpp/V3.5/d6/d59/classcocos2d_1_1_lua_objc_bridge.html)
- [关于 iTunes Connect](https://developer.apple.com/library/content/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide_zh_CN/Chapters/About.html#//apple_ref/doc/uid/TP40016325-CH1-SW1)
- [中文版 排行榜及成就 配置属性](https://developer.apple.com/library/content/documentation/LanguagesUtilities/Conceptual/iTunesConnectGameCenter_Guide_SCh/Chapters/Leaderboards.html#//apple_ref/doc/uid/TP40014490-CH2-SW1)


<br/>
<br/>
<br/>

### 参考

- https://www.raywenderlich.com/23189/whats-new-with-game-center-in-ios-6
- http://www.jianshu.com/p/4279f84d8340
- http://blog.csdn.net/shenjie12345678/article/details/45025403/
