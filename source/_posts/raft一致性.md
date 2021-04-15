---
title: raft一致性协议基础
category: 数据库
tag: [分布式数据库]
thumbnail: /img/8-24-1.png
---

以下内容理解基于动画展示, 对于raft一致性能够直观的理解.

http://thesecretlivesofdata.com/

<!--more-->

## 一致性

用户对不同服务器的读取, 内容需要一致.

挂掉的服务器在恢复时, 需要恢复到与其他服务器一致

两地集群 之间难通信, 如何保证数据一致

## raft 三角色

leader, follower, candidate

## raft 事件

* 日志复制

user -> leader 我要更新啦!?

leader -> follower 准备更新x=5? 将日志传给follower

follower -> leader x=5 we are ready!

leader 更新 ->follower 我更新啦

follower 好, 我也更新

* 领导人选举

超时线程: 150ms - 300ms 各自随机

心跳线程: 老大还活着!!!

某follower timesup 成为 candidate, 对自己投票, term更新+1, 问其他follower是否投票

其他follower收到信息, 更新term, 同时vote, 同时更新自己的超时线程.

成为leader之后持续发送心跳(也包含一些数据), follower收到心跳, 则更新自己的超时线程(老大还没死呢? 不要挣当话事人!!!)

leader死亡之后, follower超时, 成为candidate, 重复上述历程.



分割投票: 两个节点同时timesup

假如两个节点投票数也一致咋办? 

2 个 candidate 处于 term4, 2个follower收到信息之后更新为term4, 然而在term4内没有产生leader, 因此也没有心跳包, 因此follower超时线程没有清零. 其中一个follower超时后进入term5, 重新发起投票, 成为leader.

* 数据同步

leader通过心跳包传递日志, 

* 网络分区的一致性

A区B区仅内部通讯.

一旦服务器被划区域分布. 没有leader的集群由于没有收到心跳包, 超时线程无人清零, 将选举新的领导人. 尽管存在多个leader, 但是term存在先后记录.对于不同分区的数据库user更新, (每个数据库都知道全局数据库num), 只有占大多数的数据库集群(leader票数)会提交日志更新.

当网络分区恢复, 两方leader都在发送心跳包, 谁的term大听谁的. 少数派的未提交日志被回滚, 同时匹配新的日志



>  心远的疑问?
>
> ​	网络分区前, 集群term为统一值. 当网络分区时, 无leader的集群会发起新的投票, 之后term+1. 假如该集群不是大多数, 则不会提交日志更新. 最后网络恢复后重新回归一致性?
>
> ​	一致性的设计原则到底是什么呀? 当网络分区时, 到底哪边用户的更新被保存/丢弃呢?
>
> ​	假如我们的原则是: 网络分区时, 每个分区自己可用. 合并时按照大多数原则合并, 则存在矛盾!
>
> ​	最终我确定: 网络分区合并时, 是按照无leader集群日志匹配!!!



然而raft协议仅仅只是这样嘛? 具体实现还有哪些策略呢? 未完待续...