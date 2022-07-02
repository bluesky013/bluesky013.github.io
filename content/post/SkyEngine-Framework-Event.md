---
title: Framework-Event
date: 2022-07-02
authorbox: false
categories:
  - "Blog"
tags:
  - "Real Time Rendering"
  - "SkyEngine"
  - "Framework"
---

## Event
引擎存在大量的监听事件，典型的比如：  
* 系统Tick事件、初始化、销毁等事件
* 窗口事件  

为此可能存在大量分散的 Listener 处理、维护，因此设计了一个统一的 Event 系统来统一处理。  
基础功能包括：
* [x] Event 对象通过模板 + Interface 方式确定一个单例对象
* [x] Event 支持 Listener 针对某一个类型 Key 进行过滤
* [x] Event 支持对 Listener 进行广播

使用形式
```c++
// Interface
class EventInterface : public EventTraits {
    virtual void OnEvent0(float v) = 0;
    virtual void OnEvent1() = 0;
};

// Listener
class Object : public EventInterface {
public:
    Object()
    {
        Event<EventInterface>::Connect(this);
    }
    
    ~Object()
    {
        Event<EventInterface>::DisConnect(this);
    }

    void OnEvent0(float v) { /* todo */ }
    void OnEvent1() { /* todo */ }
};

// Dispatcher
{
    // Event0
    Event<ITestEvent1>::BroadCast(&EventInterface::OnEvent0, 1.f);
    
    // Event1
    Event<ITestEvent1>::BroadCast(&EventInterface::OnEvent1);
}

```

在一定程度上参考 [o3de](https://github.com/o3de/o3de) EBus设计
实现上主要需要解决以下几个问题：
1. 类型判断
2. Listener 存储
3. 接口调用

其中存储、调用都取决于类型
* 若无过滤模式，则采用一个线性存储空间就足够，若需要按照 Key 过滤则更适合 Map 存储。
* 若无过滤模式，则 BroadCast 无需指定 Key，否则需要额外的 Key 参数。

参考 EBus，EventInterface 通过继承一个 EventTraits，利用静多态对 Event 模板做特化处理，见 EventTraits。
其中 KeyType 用于 Event 模板的 Storage 特化，MutexType 预留用于确定 Storage 是否需要线程安全。 
```c++
struct EventTraits {
    using KeyType = void;
    using MutexType = void;
};

template <typename Interface, class KeyType = typename Interface::KeyType>
class Event {
public:
    // ...
    class Storage : public Singleton<Storage> {
    public:
        // ...
    private:
        std::map<KeyType, std::set<Interface*>> listeners;
    };
};

// 非过滤模式偏特化
template <typename Interface>
class Event<Interface, void> {
public:
    // ...
    class Storage : public Singleton<Storage> {
    public:
        // ...
    private:
        std::vector<Interface*> listeners;
    };
};
```
