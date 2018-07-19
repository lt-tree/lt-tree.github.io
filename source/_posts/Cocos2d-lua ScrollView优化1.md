---
title: Cocos2d-lua ScrollView优化1
date: 2018-05-21 23:44:35
tags: [cocos2d, 想就做]

---

cocos2d lua 
修改ScrollView第一弹: item

<!-- more -->

<br/>

# 做了什么

## 问题：
在使用ListView的时候，有多少个数据就会创建多少个item，并不会重复利用或回收释放。
随着数据量的增加，会对性能造成很大的影响。

## 解决方案：
* clone 改成 create[据说是这样，我没有测试过 = =...]
我们在使用ListView的时候，创建一个item，是通过lua重写的pushBackCustomItemView，
它会先调用ListView的pushBackDefaultItem，通过clone创建一个csb，我们再把数据赋过去。
所以，我们完全可以create一个csb，相对于clone会快一些。

 缺点： 这个应该会有点效果，嗯，有点效果而已。

* 分帧加载（逐帧加载）
并不是在一帧全加载完，而是选择每帧加载一定个数，直到加载完成

 缺点： 通过lua现有的协程来实现，但是流畅度不是很好，刚进入界面的时候可能看到item是逐渐加载进来的。

* 异步加载
这个主要对一些图片多的item，我们如果需要切换图，可以通过异步加载，等图片加载完再换图，这样不影响之后item的加载。

 缺点： 会看到 默认图（csb创建的样子） -> 真正效果的转换过程。

* 滑动到底加载
就是先加载一定数量，监听到底部了，再拉取后面的部分，直到全部加载完。

 缺点： 做一系列监听滑动等，没有根本解决问题。


* 重用item[本次实现的方法]
其实，上面的那些方法，都是优化的技巧，并没有从根本上解决问题。
我们要根本的解决问题，就是创建可视区域可容纳数量+1的item，然后不断重用这些item。
在ListView同一时刻，只能见到5个item，那我就创建6个item，然后不断重用这些item。


<br>

# 2.怎么做的
## 机制
首先明确view与inner，
view像一个窗口，它的大小就是我们可以见到的大小（当然要设置裁切），
inner是我们创建的所有item添加的地方（item并不是加载ScrollView上，而是加在了inner上）
ScrollView/ListView会监听滑动，同时相应的移动inner的位置，从而让我们看到item位置的变化。
**简而言之，item加载inner上，是inner动，不是view动。**

## 想法
在ScrollView或者ListView中，正常情况是这样的：
(前面数字代表item位置，后面数字代表item， ----代表可视区域)


```lua
		1   1                   1
		    ----
		2   2                   2
		3   3                   3
		                        ----
		4   4                   4
		    ----                
		5   5                   5
		6   6                   6
		                        ----
		7   7                   7
		8   8                   8
```

可以发现，
前面的例子中, 只能看见2, 3, 4; 但是看不见的1, 5, 6, 7, 8 依旧存在
后面的例子中, 只能看见4, 5, 6; 但是看不见的1, 2, 3, 7, 8 依旧存在

所以，我们改成下面的样子:

```lua
		1                        
		    ----
		2   2                    
		3   3                    
		                        ----
		4   4                   4
		    ----                
		5                       5
		6                       6
		                        ----
		7                        
		8                        
```

因为可视区域只有3个item，我们就创建3个item，然后不断重用它们。(当然实际操作中，需要多创建一个，否则有穿帮风险)
但是，位置，我们依旧留着（划重点，**inner大小不变**，否则无法滑动），
在往下滑的时候，最上面的跑到下面去顶替下面的item；
往上滑的时候，最下面的跑到上面去顶替上面的item；

## 做法
实现方法，
可以通过监听ScrollView滑动，每当ScrollView滚动，我们可以知道当前inner位置，
然后知道item的位置，从而判断item需不需要移动位置。
这里，用的是编辑一个绘制方法，每隔一段时间，都看一下各个item位置，然后根据需求移动位置。
我们在加载csb的时候将ScrollView记录下来，在view的update中调用它。
（本来想重写update，但是遇到了一些问题，所以妥协用了它，具体可以看后面 遇到的问题）


init:

```lua
	    --[[
	        name            :   item类名 
	        totalItemNum    :   item总数
	        ...             :   创建item时需要的参数
	    --]]
	    function ScrollView:setItemViewModel(name, totalItemNum, ...)
```

主要代码：


```lua
        -- 得到所需绘制item个数
        local count = math.ceil(self:getContentSize().height / self.tItemContentSize.height) + 1
        for i = 1, count do
            -- 创建item
            local view = CSBReaderLoad(name)
            view:init(...)
            if view.setIndex then
                view:setIndex(i)
            end

            -- 将父节点转为widget类型（原因可见 遇到的问题）
            local widget = quick.packageNodeToWidget(view.pLayer:getChildByName("LayerTouch"))
            view.pLayer = widget
            -- widget随父节点透明度变化，默认是false
            widget:setCascadeOpacityEnabled(true)
            self:addChild(view.pLayer)

            -- 总数小于所需绘制item个数，需要隐藏多创建的
            view.pLayer:setVisible(i > 0 and i <= self.totalItemNum)
            -- 设置位置，注意我们加item是从下往上加的
            view.pLayer:setPositionY(self.tItemContentSize.height * (self.totalItemNum - i))
            table.insert(self.tItemView, view)
        end
```

update:

```lua
	    function ScrollView:updateView(dt)
```

主要代码：

```lua
        -- 控制刷新时间
        self.updateTimer = self.updateTimer + dt
        if (self.updateTimer < self.updateInterval) then
            return 
        end
        self.updateTimer = 0;

        ...

        -- 遍历所有创建的item，如果它们需要移动位置，就移动它们的位置，并让它们重绘自己
        for i = 1, length do
            local item = self:getItemView(i)
            local viewPos = self:getPositionInView(item.pLayer);
            if increaseVal < 0 then
                if viewPos.y < minBoundary then
                    if item.index and item.setIndex then
                        item:setIndex(item.index - length)
                        item.pLayer:setVisible(item.index > 0 and item.index <= self.totalItemNum)
                    end

                    item.pLayer:setPositionY(item.pLayer:getPositionY() + offset)
                end
            elseif increaseVal > 0 then
                if (viewPos.y > maxBoundary and item.pLayer:getPositionY() - offset > -self.tContentSize.height) then
                    if item.index and item.setIndex then
                        item:setIndex(item.index + length)
                        item.pLayer:setVisible(item.index > 0 and item.index <= self.totalItemNum)
                    end
                    
                    item.pLayer:setPositionY(item.pLayer:getPositionY() - offset)
                end
            end
        end
```

<br/>

# 3. 遇到的问题
## 关于update
在3.x中lua启用定时器有两种方法：

第一种方法 scheduleUpdateWithPriorityLua

```C++
		scheduleUpdateWithPriorityLua(update, priority)
			update - 刷新函数，
			priority - 优先级，
```

此方法在Node类中实现，所以它的子类都可以使用。
此方法默认为每帧都刷新因此，无法自定义刷新时间。
这里，没有用这个方法，是因为ScrollView自己已经实现了update方法。
所以，当我们重新注册给ScrollView一个update的时候，发现无法替换。
这里涉及到计时器存储刷新方法：
刷新方法通过哈希表存储，在主循环期间，不移除已有方法，而是将它暂停，且恢复时不加载新方法，而是将原有方法恢复。

启用定时器的源码如下：

```C++
void Node::scheduleUpdateWithPriorityLua(int nHandler, int priority)
{
    unscheduleUpdate();
    
#if CC_ENABLE_SCRIPT_BINDING
    _updateScriptHandler = nHandler;
#endif
    
    _scheduler->scheduleUpdate(this, priority, !_running);
}
```

执行： unscheduleUpdate();
会先判断节点是否有update方法，在哈希表中查找，并执行移除方法：

```C++
		tHashUpdateEntry *element = nullptr;
		HASH_FIND_PTR(_hashForUpdates, &target, element);
		if (element)
		{
		    if (_updateHashLocked)
		    {
		        element->entry->markedForDeletion = true;
		    }
		    else
		    {
		        this->removeUpdateFromHash(element->entry);
		    }
		}
```

上面移除方法，会根据_updateHashLocked值来执行，
它为真时，
如果节点原来有update，就先废弃它，废弃的方法是，将它标记为已删除，并让它暂停。注意！这里并没有真正的删除，而是将他表示是否删除的字段改值。
它为假时，
直接从哈希表中移除update方法。

执行： 
```C++
	_scheduler->scheduleUpdate(this, priority, !_running);
```

加入update，也会先从哈希表中查找update，再执行添加方法。

```C++
		tHashUpdateEntry *hashElement = nullptr;
		HASH_FIND_PTR(_hashForUpdates, &target, hashElement);
		if (hashElement)
		{
		    // check if priority has changed
		    if ((*hashElement->list)->priority != priority)
		    {
		        if (_updateHashLocked)
		        {
		            CCLOG("warning: you CANNOT change update priority in scheduled function");
		            hashElement->entry->markedForDeletion = false;
		            hashElement->entry->paused = paused;
		            return;
		        }
		        else
		        {
		            // will be added again outside if (hashElement).
		            unscheduleUpdate(target);
		        }
		    }
		    else
		    {
		        hashElement->entry->markedForDeletion = false;
		        hashElement->entry->paused = paused;
		        return;
		    }
		}
```

添加方法，会先判断优先级，如果优先级相同，那么就恢复原来的update。
否则，根据 \_updateHashLocked 值执行接下来操作。

从移除和添加可以发现，关键值在于 \_updateHashLocked的值，
这个值在Scheduler::update中设置，开始的时候设置为true，最后结束设置为false。
所以，如果要修改，就很麻烦，就放弃用这个方法了。
*道理同样适用于所有自己已经重写了update，想要更换update情形*



第二种方法，通过定时管理器调用
就是上面指的Scheduler,不过我们不调ScrollView的，而是创建一个新的。

```lua    
scheduler:scheduleScriptFunc(update, inteval, isOnce)
	scheduler - cc.Director:getInstance():getScheduler()
	update - 更新方法
	inteval - 刷新时间间隔
	isOnce - 是否只执行一次
```

注意，如果用这个方法，需要负责创建，也要负责移除。
上面方法会返回一个id，之后可以通过这个id来删除它。

```lua
		cc.Director:getInstance():getScheduler():unscheduleScriptEntry(id)
```

## 为什么要把item包装成Widget
在刚开始往ScrollView加child时，方法是将item的Node直接往ScrollView addChild（ScrollView封装了它，其实就是往inner addChild）
但是当直接addChild时，会产生很多问题：比如按钮吞噬触摸，无法滑动等等。

那就要问一下了，为什么ListView没事呢？
这其实是Cocos对继承自ccui.Widget的事件的处理。
所有的控件事件监听都是单点触摸，并且会吞噬事件。

```C++
		_touchListener = EventListenerTouchOneByOne::create();
		CC_SAFE_RETAIN(_touchListener);
		_touchListener->setSwallowTouches(true);
		_touchListener->onTouchBegan = CC_CALLBACK_2(Widget::onTouchBegan, this);
		_touchListener->onTouchMoved = CC_CALLBACK_2(Widget::onTouchMoved, this);
		_touchListener->onTouchEnded = CC_CALLBACK_2(Widget::onTouchEnded, this);
		_touchListener->onTouchCancelled = CC_CALLBACK_2(Widget::onTouchCancelled, this);
		_eventDispatcher->addEventListenerWithSceneGraphPriority(_touchListener, this);
```

在widget的onTouchBegan, onTouchMove, onTouchEnd中，都会调用 propagateTouchEvent,
这个方法是传播事件，每个子节点会吞噬事件，自己处理完，再向父节点传递，一般ScrollView、ListView、PageView会处理这些事件。

```C++
		Widget* widgetParent = getWidgetParent();
		if (widgetParent)
		{
		    widgetParent->_hittedByCamera = _hittedByCamera;
		    widgetParent->interceptTouchEvent(event, sender, touch);
		    widgetParent->_hittedByCamera = nullptr;
		}
```

可以看出，只有继承自Widget类的，才会接收到interceptTouchEvent,并进行处理。
而且，ScrollView的interceptTouchEvent 已经处理好了按钮的点击，取消等效果。

```C++
		void ScrollView::interceptTouchEvent(Widget::TouchEventType event, Widget *sender,Touch* touch)
		{
		    if(!_touchEnabled)
		    {
		        Layout::interceptTouchEvent(event, sender, touch);
		        return;
		    }
		    if(_direction == Direction::NONE)
		        return;
		    Vec2 touchPoint = touch->getLocation();
		    switch (event)
		    {
		        case TouchEventType::BEGAN:
		        {
		            _isInterceptTouch = true;
		            _touchBeganPosition = touch->getLocation();
		            handlePressLogic(touch);
		        }
		        break;
		        case TouchEventType::MOVED:
		        {
		            _touchMovePosition = touch->getLocation();
		            // calculates move offset in points
		            float offsetInInch = 0;
		            switch (_direction)
		            {
		                case Direction::HORIZONTAL:
		                    offsetInInch = convertDistanceFromPointToInch(Vec2(std::abs(sender->getTouchBeganPosition().x - touchPoint.x), 0));
		                    break;
		                case Direction::VERTICAL:
		                    offsetInInch = convertDistanceFromPointToInch(Vec2(0, std::abs(sender->getTouchBeganPosition().y - touchPoint.y)));
		                    break;
		                case Direction::BOTH:
		                    offsetInInch = convertDistanceFromPointToInch(sender->getTouchBeganPosition() - touchPoint);
		                    break;
		                default:
		                    break;
		            }
		            if (offsetInInch > _childFocusCancelOffsetInInch)
		            {
		                sender->setHighlighted(false);
		                handleMoveLogic(touch);
		            }
		        }
		        break;

		        case TouchEventType::CANCELED:
		        case TouchEventType::ENDED:
		        {
		            _touchEndPosition = touch->getLocation();
		            handleReleaseLogic(touch);
		            if (sender->isSwallowTouches())
		            {
		                _isInterceptTouch = false;
		            }
		        }
		        break;
		    }
		}
```

之前的方法有问题，就是因为直接将Node addChild到ScrollView，当触摸传递到Node，发现无法转成Widget对象，就放弃了向上传播事件。
所以，需要将item包装成Widget来让它将事件传递给ScrollView。

<br/>

# 4. 总结
怎么用这个呢？

1. 调用 ScrollView:setItemViewModel(item, item总数, 创建item所需的额外参数)
2. 所有的item要有方法 item:setIndex(index), 并且以 self.index 作为自己的index[这里可以写一个类来封装，让所有item都继承它]
3. 在删除的时候，要将ScrollView的每帧更新方法移除 


现在，ScrollView已经可以重用item了。
但是，还是比较粗糙；做为一个控件，仅仅是这样可不行。
之后，会对这个控件慢慢优化，让它支持更多的功能，更加得心应手。

















