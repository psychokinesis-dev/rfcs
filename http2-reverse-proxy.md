- 开始日期： 2016-03-08

# 概述

使用 HTTP/2 协议实现 Web 服务器之间的互相连接，进而支持可以访问其中任何一台 Web 服务器的浏览器都可直接访问位于其他 Web 服务器上的内容，大致原理如下：

![](http2_reverse_proxy.png)

网络中所有节点都是对等关系，以某个节点 Self 为基准可将网络中的节点分为三类： Entry、 Guest 和 Peer 。其中 Peer 泛指网络中所有的节点， Self、 Entry 和 Guest 都是特殊的 Peer ； Entry 是 Self 加入这个网络的唯一入口，但 Entry 并不是一个固定的节点，它随着网络的变化而动态改变，当 Self 成为初始节点时则 Entry 不存在； Guest 是通过 Self 加入这个网络的节点，它并不是唯一的， Self 可以决定它允许多少个 Guest 通过自身加入网络。

网络中所有节点都提供 HTTP 和 HTTP/2 端口， HTTP 用于外部请求， HTTP/2 用于上述构建网络的过程。

# 目的

实现**对等的去中心化网络结构**。

# 实现细节

基于 HTTP/2 ，需要实现以下功能：

## 与 Entry 的连接
### 确定 Entry 节点
Self 节点启动时会从指定的地址获取到 Entry 节点列表（可能为空），然后依次尝试建立连接。当 Entry 节点被删除时则随机选定一个来源于 Entry 节点的其他节点作为新的 Entry，开始尝试连接。当没有 Entry 可供选择时会定期尝试从指定地址获取 Entry 节点列表。

### 建立连接
Self 节点与 Entry 节点之间的 HTTP/2 连接通过如下方法建立：首先 Entry 节点运行一个 HTTP/2 服务器，然后 Self 节点通过 HTTP/2 请求与之建立连接，连接建立之后 Entry 节点通过 Server Push 发送消息，而 Self 节点则通过 HTTP/2 请求发送消息。

## 节点状态
### 状态同步
每个节点仅向 Entry 和 Guest 节点通知自身已知的所有节点状态信息，每个节点收到其他节点的状态信息后需要更新到本地，包括节点名、状态、更新时间、来源和版本信息。通知的动作仅在节点有增删时触发。

### 节点的增加
当有 Guest 节点通过 Self 加入网络时触发 Guest 节点增加事件。

### 节点的删除
当 Entry 主动或被动（状态检测失败）断开连接时触发 Entry 节点删除事件；而当 Guest 断开连接时会同时触发 Guest 及来源为 Guest 的所有节点删除事件。

### 状态检测
Self 需要定期向 Entry 发送心跳检测，当超过一定次数没有响应时则 Entry 检测失败；与此同时当 Guest 一定时间内都没有心跳检测请求时则认为 Guest 检测失败。


## 基于 URL 的寻址
通过 URL 路由请求到对应的节点上执行相应的操作。

### 节点的寻址（ /peer ）
在 peer 之后指明节点名。比如对于 URL ： http://127.0.0.1:8181/peer/ajiudwefe321ff323ferg/test ，即需要将此请求路由到节点 ajiudwefe321ff323ferg 上。

## 处理外部 HTTP 客户端请求
外部 HTTP 请求到达节点后会转换为内部的 HTTP/2 请求处理。

# 未解决的问题
## 节点名的安全性问题
节点名缺乏所有权，存在被冒用的问题，将来可以考虑使用区块链技术解决。
