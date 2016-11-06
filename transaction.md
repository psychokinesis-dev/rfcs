- 开始日期： 2016-11-06

# 概述

在 [Psychokinesis](https://github.com/psychokinesis-dev/js-psychokinesis) 的基础上通过 Bitcoin 实现网络资源交易。交易过程需要三方介入：资源拥有者、请求资源者和中介。资源拥有者发布信息到中介，请求资源者从中介查找到所需资源，然后向中介请求资源并支付 Bitcoin ，中介收到请求后将其转发给资源拥有者，资源拥有者提供资源由中介转发给请求资源者，完成后中介给资源拥有者支付 Bitcoin 。大致流程如下：
![](transaction.png)

# 目的

让 Psychokinesis 网络支持资源交易，提升网络中资源的流动性。

# 实现细节

# 类似的设计
[JoyStream](http://joystream.co/) 通过支付 Bitcoin 来加快下载上传的 BitTorrent 客户端。

# 缺陷
