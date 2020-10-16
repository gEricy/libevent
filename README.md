# libevent简介
1. libevent是基于事件驱动的网络库，支持IO、定时器、信号等事件。实现了带缓冲的bufferevent。它是一种经典的Reactor模式。
2. 主要特点：把代码用回调函数的方式注册成一个个“驱动”，使得代码解耦，看起来很清晰（比如：对于不同的IO多路复用epoll/select等）
3. 核心思想：(1)首先要创建一个事件根基结构体event_base，它相当于是一个插座，它其实是一个epoll红黑树；(2)所有的事件都可以插在该插座上，由该插座监听所有注册的事件，等事件触发时调用该事件注册的回调函数

# 学习建议
- 之前研究生期间，跟过网课，浅显的看过源码，但是没有深究，记录了一些笔记，见CSDN博客（主要以介绍原理为主，想深入还是要扎进源代码中）：https://blog.csdn.net/weixin_36750623/category_8281007.html
- 读源代码，首先要有“驱动”接口思想，以及一些最基本的原理，如：epoll实现原理、反应堆模式Reactor、缓冲区、信号Signal、缓冲区等概念

# 统一事件源
1. 超时事件
- 采用小根堆的方式（还有2种经典的超时事件实现，即：Nginx的红黑树、libco协程库的时间轮）
- 采用epoll_wait等IO多路复用接口的超时参数，当超时时，该函数返回，取出时间堆中超时的事件处理之
2. I\O事件
- epoll_wait等IO多路复用接口监听文件描述符fd读写方式
3. 信号事件
- 采用本地全双工管道socket_pair，结合信号signal函数（基本流程参考`图_signal事件流程`）
![image text](https://github.com/gEricy/libevent/blob/master/%E5%9B%BE_signal%E4%BA%8B%E4%BB%B6%E6%B5%81%E7%A8%8B.png)
- 将signal事件作为读写I\O事件处理
