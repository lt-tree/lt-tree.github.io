---
title: Cocos2d-lua ScrollView优化2
date: 2018-06-05 23:44:35
tags: [cocos2d, 想就做]

---

cocos2d lua 
修改ScrollView第二弹: 功能扩展

<!-- more -->

<br/>

---

温故：
上回书说到, 对Cocos2d-lua的ScrollView进行了修改优化。
主要做了 —— 重用item。
仅仅是重用item, 是远远不够的；还要对它进行进一步的功能扩展。

---

<br/>

# 概览
这次的扩展包括：
- 支持横向和竖向
- 支持多行多列
- item的适配
- item数量不够时的居中
- 刷新数据
- 跳转到指定item
- 飞入动画

<br/>

## 多方向
之前的版本仅仅是纵向而已, 当然要支持横向的滑动了。
横向滑动其实与纵向不同。

### 纵向
由于ScrollView锚点在(0, 0), 要针对这个做一些处理。
否则, 显示的是如下的样子：


        ...
        5
        4
        3
        2
        1


从下往上排列, 而且滑动是从下往上滑。
显然, 这并不符合常规操作。
正常应该是, 从上往下滑, 且:


        1
        2
        3
        4
        5
        ...


所以, 需要对它的坐标进行小处理。
这里有两个坐标需要被处理：
	
- item（要求锚点为(0, 0))
    它正常坐标是从(0, 0)开始, 然后随着索引增加变为: (0, itemSize.height * index)
    修改后的坐标应该是从(0, innerSize.height - itemSize.height)开始, 随着索引增加变为:(0, innerSize.height - itemSize.height * index)

- inner
    正常开始的坐标为(0, 0), 显示的是最底部的信息, 随着滑动y坐标减少。
    修改后坐标为(0, scrollviewSize.height - innerSize.height), 显示最顶部的信息, 随着滑动y坐标增加。


### 横向
横向就没有那么多问题了, 很符合常规的动作。


        1 	2 	3 	4 	5 	...


它的两个坐标就不需要处理:

- item(要求锚点为(0, 0))
	坐标从(0, 0)开始, 随着索引增加变为: (itemSize.width * index, 0)

- inner
	坐标从(0, 0)开始, 随着滑动x坐标增加


### 主要代码
    

        local ScrollViewDirection = {
            DIR_VERTICAL = 1,
            DIR_HORIZONTAL = 2,
            DIR_BOTH = 3,
        }

        -- ScrollView 的大小
        self.tContentSize
        -- Item 的大小
        self.tItemContentSize
        -- item总数
        self.iTotalItemNum 
        -- 重用的item的集合
        self.tItemView

        if ScrollViewDirection.DIR_HORIZONTAL == self:getDirection() then
            self.tContentSize.width = self.tItemContentSize.width * self.iTotalItemNum
            self:getInnerContainer():setContentSize(self.tContentSize)
            self.fLastContentPos = self:getContentSize().width - self.tContentSize.width

            local count = math.min(self.iTotalItemNum, math.ceil(self:getContentSize().width / self.tItemContentSize.width) + 1)
            for i = 1, count do
                local view = CSBReaderLoad(name)
                view:init(...)
                if view.setIndex then
                    view:setIndex(i)
                end

                -- 将父节点转为widget类型（原因可见 上一篇文章）
                local widget = quick.packageNodeToWidget(view.pLayer:getChildByName("LayerTouch"))
                view.pLayer = widget
                -- widget随父节点透明度变化，默认是false
                widget:setCascadeOpacityEnabled(true)
                self:addChild(view.pLayer)

                view.pLayer:setVisible(i > 0 and i <= self.iTotalItemNum)
                view.pLayer:setPosition(self.tItemContentSize.width * (i - 1), 0)
                table.insert(self.tItemView, view)
            end

            self:jumpToLeft()
            self:getInnerContainer():setPositionX(self.fLastContentPos)
            self:setTouchEnabled(self.tContentSize.width > self:getContentSize().width)

        elseif ScrollViewDirection.DIR_VERTICAL == self:getDirection() then
            self.tContentSize.height = self.tItemContentSize.height * self.iTotalItemNum
            self:getInnerContainer():setContentSize(self.tContentSize)
            self.fLastContentPos = self:getContentSize().height - self.tContentSize.height

            local count = math.min(self.iTotalItemNum, math.ceil(self:getContentSize().height / self.tItemContentSize.height) + 1)
            for i = 1, count do
                local view = CSBReaderLoad(name)
                view:init(...)
                if view.setIndex then
                    view:setIndex(i)
                end

                -- 将父节点转为widget类型（原因可见 上一篇文章）
                local widget = quick.packageNodeToWidget(view.pLayer:getChildByName("LayerTouch"))
                view.pLayer = widget
                -- widget随父节点透明度变化，默认是false
                widget:setCascadeOpacityEnabled(true)
                self:addChild(view.pLayer)

                view.pLayer:setVisible(i > 0 and i <= self.iTotalItemNum)
                view.pLayer:setPositionY(self.tItemContentSize.height * (self.iTotalItemNum - i))
                table.insert(self.tItemView, view)
            end

            self:jumpToTop()
            self:getInnerContainer():setPositionY(self.fLastContentPos)
            self:setTouchEnabled(self.tContentSize.height > self:getContentSize().height)
        end


## 适配item
根据ScrollView显示区域大小及方向, 适当调整item大小。
更充分重用item, 适应多尺寸item。
如果是纵向的ScrollView, 根据width的值, 来决定放缩值。
如果是横向的ScrollView, 根据height的值, 来决定放缩值。
然后根据放缩值再修改一下item size的值。

### 修改的东西(以纵向滑动ScrollView为例)

- ScrollView inner 大小


        local scale = ScrollViewSize.width / (ItemSize.width * multiNum)


- 需要绘制item的总个数


        local totalRow = cond(totalItemNum % multiNum == 0, 
            totalItemNum / multiNum,
            math.ceil(totalItemNum / multiNum))


- item的位置


        self.iCount = math.min(totalRow, math.ceil(ScrollViewSize.height / ItemSize.height) + 1)
        for i = 1, self.iCount do
            for j = 1, self.iMultiNum do
                ...

                item:setPosition(ItemSize.width * (j - 1), ItemSize.height * (totalRow - i))
            end 
        end


## 多行多列
重用item, 这么棒的东西, 肯定要多用用呀。
支持多行多列，是根据ScrollView的滚动方向，再根据传入的行/列值进行设置。
需要重新计算一些数值。(下面均以纵向滑动的ScrollView为例)

### init 

* 放缩值
    

    	scale = innerSize.width / (itemSize.width * multiNum)


* inner size


    	-- 根据总共需要的行数来计算高度
    	totalRow = (totalItemNum % multiNum == 0) and (totalItemNum / multiNum) or (math.ceil(totalItemNum / multiNum))
    	innerSize.height = totalRow * itemSize.height


* item position


    	-- 获得需要重用的行数
    	showRow = math.min(totalRow, math.ceil(viewSize.height / itemSize.height) + 1)
    	for i = 1, showRow do
    		for j = 1, multiNum do
    			...

    			view:setPosition(itemSize.width * (j - 1), itemSize.height * (totalRow - i))
    		end
    	end


## item数量不够时的居中
主要是有个需求，希望item没有填满view的时候，所有的item居中显示。
其实，item还是按照原来的方式放置，只需要移动inner的位置即可。


        --[[
            描述:
                ScrollView内Item是否居中显示
            参数:
                isCenter - boolean
                    是否居中显示
            返回:
                无
        --]]
        function ScrollView:setShowCenter(isCenter)
            local viewSize = self:getContentSize()
            if ScrollViewDirection.DIR_HORIZONTAL == self:getDirection() then
                local dertaValue = viewSize.width - self.tContentSize.width
                if isCenter then
                    if dertaValue > 0 then
                        self:getInnerContainer():setPositionX(dertaValue/2)
                    end
                else
                    self:getInnerContainer():setPositionX(dertaValue)
                end
            elseif ScrollViewDirection.DIR_VERTICAL == self:getDirection() then
                local dertaValue = viewSize.height - self.tContentSize.height
                if isCenter then
                    if dertaValue > 0 then
                        self:getInnerContainer():setPositionY(dertaValue/2)
                    end
                else
                    self:getInnerContainer():setPositionY(dertaValue)
                end
            end
        end


## 刷新数据
创建完ScrollView，除非item变动自己的位置，否则是不会刷新数据的。
所以需要一个手动刷新的方法。
这里充分利用了lua的变长参数，在配合人为默认规定。ie


        --[[
            描述:
                刷新ScrollView中指定索引的item
            参数:
                ... - 传入一堆int
                    item的索引, -1代表全部刷新
            返回:
                无
        --]]
        function ScrollView:refreshItems(...)
            local args = {...}

            if #args > 0 then
                if args[1] == -1 then
                    local items = self:getAllItemView()
                    for k, v in pairs(items) do
                        v:setIndex(v.iIndex)
                    end
                else
                    -- 先做一个映射表，便于查找是否需要更新
                    local tempTable = {}
                    for k, v in pairs(args) do
                        tempTable[v] = 1
                    end

                    local items = self:getAllItemView()
                    for k, v in pairs(items) do
                        if v.iIndex and tempTable[v.iIndex] == 1 then
                            v:setIndex(v.iIndex)
                        end
                    end
                end
            end
        end


这里我用了一个映射表。
否则需要嵌套两层循环，复杂度 m * n
做一个映射，只需要 n + m
用空间来换取时间

## 跳转到指定item
这个功能ListView是支持的，觉得ScrollView也有必要支持一下。
方法是先计算出inner需要移动多少距离，从而知道了index需要变化多少。

### 主要步骤:（也是以垂直滑动方向为例）

	1. 计算所需跳转的index在最上方位置是第几行
	2. 计算inner需要滑动多少距离
	3. 计算从当前到目标，index需要变动多少
	4. 按照移动后的index，重新布局item


### 主要代码：


        -- -- 步骤1
        local line = (index % self.iMultiNum == 0) and
            (index / self.iMultiNum) or
            (math.ceil(index / self.iMultiNum))

        -- -- 步骤2
        local posY = self:getContentSize().height - self.tContentSize.height + self.tItemContentSize.height * (line - 1)
        -- 要考虑到滑动到底部，无法继续向上滑的情况
        posY = (posY > 0) and 0 or posY

        -- -- 步骤3
        local changeIndex = math.ceil((posY - self:getInnerContainer():getPositionY()) / self.tItemContentSize.height)
        -- inner跳到指定位置
        self:jumpToDestination(cc.p(0, posY))

        -- -- 步骤4
        self:updateViewByChangeIndex(changeIndex * self.iMultiNum)


### 根据index，重新布局item
	

        --[[
            描述:
                根据index，重新布局item
            参数:
                changeIndex:   int
                    改变的index值
            返回:
                无
        ]]
        function ScrollView:updateViewByChangeIndex(changeIndex)
            local totalBlock = (self.iTotalItemNum % self.iMultiNum == 0) and
                (self.iTotalItemNum / self.iMultiNum) or
                (math.ceil(self.iTotalItemNum / self.iMultiNum))

            local items = self:getAllItemView()
            for k, v in pairs(items) do
                local idx = v.iIndex + changeIndex

                v:setIndex(idx)
                v.pLayer:setVisible(idx > 0 and idx <= self.iTotalItemNum)
                
                local i = (idx % self.iMultiNum == 0) and (idx / self.iMultiNum) or (math.ceil(idx / self.iMultiNum))
                local j = self.iMultiNum - (idx % self.iMultiNum)
                v.pLayer:setPosition(self.tItemContentSize.width * (j - 1), self.tItemContentSize.height * (totalBlock - i))
            end
        end


### 跳转的item在ScrollView中的位置
需要跳转到的item在可视区域的 上、中、下 显示
首先，一定要让使用者传入出现的位置枚举，
然后在计算inner移动的位置上加上偏移量。
如果要在中间显示，需要减去（向下移动） ScrollViewSize.height/2 , 因为初始的位置是按照item在最上面计算的，减去一半高度后，还需要再加上item本身高度的一半 ItemSize.height/2。
如果在底部显示，则需要减去（向下移动） ScrollViewSize.height , 同理，需要再加回来一个item的高度 ItemSize.height。
最后，依然要判定滑动到底部，无法滑动的情况。


        SCROLLVIEW_ALIGNMENT = {
            FIRST = 1,
            MID = 2,
            LAST = 3,
        }

        local posY = self:getContentSize().height - self.tContentSize.height + self.tItemContentSize.height * (line - 1)
        if alignment == SCROLLVIEW_ALIGNMENT.MID then
            posY = posY - self:getContentSize().height / 2 + self.tItemContentSize.height / 2
        elseif alignment == SCROLLVIEW_ALIGNMENT.LAST then
            posY = posY - self:getContentSize().height + self.tItemContentSize.height
        end
        posY = (posY > 0) and 0 or posY


## 飞入动画
额外再加一个飞入动画的支持吧。
就是从外部飞入到ScrollView的效果。

方法也很简单，就是在开始的时候，让所有的item在ScrollView外部；再一个个飞入到自己本应在的位置。
依旧是以垂直向为例。


        -- 遍历所有item
        for k, v in pairs(self.tItemView) do
        	-- 记录它本来所在的位置
            local aimPos = cc.p(v.pLayer:getPositionX(), v.pLayer:getPositionY())

            -- 把它放在区域外
            v.pLayer:setPositionY(-self:getContentSize().height - self.tItemContentSize.height)
            v.pLayer:runAction(
                act.seq(
                	-- 一个个飞入
                    act.delay((k - 1) * [delay_time]),
                    act.movto([move_time], aimPos)
                    )
                )
        end


当然，也要支持多方向ScrollView，并且要支持从前端飞入还是从后端飞入。
这些都是通过改动初始位置及回弹值来实现。


        --[[
            描述:
                ScrollView内item的从外部飞入动画, 有回弹效果
            参数:
                fromBack:   boolean
                    对于垂直方向, true代表自下而上; 对于水平方向, true代表自右向左
            返回:
                无
        ]]
        function ScrollView:playFlyInAction(fromBack)
            fromBack = fromBack == nil and true    
            self.tItemView = self.tItemView or {}

           	local moveTime = 0.2
           	local delayTime = 0.1

            if self:getDirection() == ScrollViewDirection.DIR_HORIZONTAL then
                local initPos = fromBack and (self:getContentSize().width + self.tItemContentSize.width) or (-self:getContentSize().width - self.tItemContentSize.width)

                for k, v in pairs(self.tItemView) do
                    local aimPos = cc.p(v.pLayer:getPositionX(), v.pLayer:getPositionY())

                    v.pLayer:setPositionX(initPos)
                    v.pLayer:runAction(
                        act.seq(
                            act.delay((k - 1) * delayTime),
                            act.movto(moveTime, aimPos)
                            )
                        )
                end
            elseif self:getDirection() == ScrollViewDirection.DIR_VERTICAL then
                local initPos = fromBack and (-self:getContentSize().height - self.tItemContentSize.height) or (self:getContentSize().height + self.tItemContentSize.height)

                for k, v in pairs(self.tItemView) do
                    local aimPos = cc.p(v.pLayer:getPositionX(), v.pLayer:getPositionY())

                    v.pLayer:setPositionY(initPos)
                    v.pLayer:runAction(
                        act.seq(
                            act.delay((k - 1) * delayTime),
                            act.movto(moveTime, aimPos)
                            )
                        )
                end
            end
        end


<br/>

# 总结
公司用ScrollView主要是用来替代ListView，虽然主要是用ScrollView的重用item的特性。
但是还是要平滑的过渡过来，要支持ListView常用的一些接口。让这个组件更完善更好用。
当然功能扩展还没有停止，之后也会陆陆续续的更新。

