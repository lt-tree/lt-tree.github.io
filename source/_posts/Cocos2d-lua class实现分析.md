---
title: Cocos2d-lua class实现分析
date: 2018-09-18 22:30:00
tags: [瞎分析]

---

Cocos2d-lua class实现分析

<!-- more -->

<br/>

### lua中的类
lua语言中实际上来说是没有类这个概念的，但是它也可以用自己的各种机制来实现类的效果。
从概念上来说，类有三大特性：封装性、继承性、多态性。lua有元表机制。
从结构上来说，类其实就是一个键值对的集合。lua有table可以满足。

lua的表查找键值对流程如下图所示：

![lua表查找键值对流程](lua表查找键值对流程.jpg)

所以，我们通过lua的table及它的元表机制，可以在lua中实现类。


<br/>

### class
在cocos2d中，自己做了一个class方法，来作为类的机制。 文件位置: [cocos\scripting\lua-bindings\script\cocos2d\functions.lua]

```lua
function class(classname, ...)
    local cls = {__cname = classname}

    local supers = {...}
    for _, super in ipairs(supers) do
        local superType = type(super)
        assert(superType == "nil" or superType == "table" or superType == "function",
            string.format("class() - create class \"%s\" with invalid super class type \"%s\"",
                classname, superType))

        if superType == "function" then
            assert(cls.__create == nil,
                string.format("class() - create class \"%s\" with more than one creating function",
                    classname));
            -- if super is function, set it to __create
            cls.__create = super
        elseif superType == "table" then
            if super[".isclass"] then
                -- super is native class
                assert(cls.__create == nil,
                    string.format("class() - create class \"%s\" with more than one creating function or native class",
                        classname));
                cls.__create = function() return super:create() end
            else
                -- super is pure lua class
                cls.__supers = cls.__supers or {}
                cls.__supers[#cls.__supers + 1] = super
                if not cls.super then
                    -- set first super pure lua class as class.super
                    cls.super = super
                end
            end
        else
            error(string.format("class() - create class \"%s\" with invalid super type",
                        classname), 0)
        end
    end

    cls.__index = cls
    if not cls.__supers or #cls.__supers == 1 then
        setmetatable(cls, {__index = cls.super})
    else
        setmetatable(cls, {__index = function(_, key)
            local supers = cls.__supers
            for i = 1, #supers do
                local super = supers[i]
                if super[key] then return super[key] end
            end
        end})
    end

    if not cls.ctor then
        -- add default constructor
        cls.ctor = function() end
    end
    cls.new = function(...)
        local instance
        if cls.__create then
            instance = cls.__create(...)
        else
            instance = {}
        end
        setmetatableindex(instance, cls)
        instance.class = cls
        instance:ctor(...)
        return instance
    end
    cls.create = function(_, ...)
        return cls.new(...)
    end

    return cls
end
```

有几个设定键：
- \_\_cname : 类名
- \_\_create : 初始化方法
- \_\_supers : 父类集合

有几个函数：
- ctor : 构造函数
- new : 初始化函数
- create : 初始化函数（对new的一个封装，为了使用方法同C++部分一致）

<br/>

class方法：
1. 设置类名
2. 遍历传入的构造方法
- 若是 function类型，则设置为初始化方法
- 若是 table类型
	- 若是 C++的类，设置初始化方法为调用原生类的create方法
	- 若是 纯lua类，将自己添加到基类中
3. 处理自己的元表
- ctor：必须要有
- new
	- 获取instance值
	- 设置instance元表index
		- 若是 C++类，设置peer
		（*PS：peer是 tolua++提供的存储C++对象在Lua中的扩展*）
		- 若是 纯lua类，设置元表
	- 执行构造方法
	- 返回instance
- create：对new的一个封装，为了使用方法同C++部分一致
4. 处理构造函数、初始化函数的创建与封装


<br/>
<br/>

---

#### 参考
- [Quick-Cocos2d-x 中的面向对象](http://blog.justbilt.com/2017/03/04/quick-x-oop/)
- [tolua++实现分析](https://github.com/zfengzhen/Blog/blob/master/article/tolua%2B%2B%E5%AE%9E%E7%8E%B0%E5%88%86%E6%9E%90.md)