---
title: Paxos算法
date: 2018-01-03 15:40:58
tags: [一致性,分布式,算法]
category: [分布式,一致性]
---

在分布式一致性中Paxos算法是一个非常重要的算法，阿里实现的X-Paxos，腾讯的phxpaxos（开源）



#### Basic Paxos

Paxos四个角色：

- Client：议题提出者
- Proposer：提议者
- Acceptor：投票者
- Learner：决策者

{% asset_img 1.png Basic Paxos %}

  图中只是显示一个提议的执行过程，步骤是

1. Client（提议发起者）提出一个提议，交给一个Propser
2. 这个Propser带着一个值（可以理解为id）去告诉所有的Acceptor
3. Acceptor看看自己的id，发现为空，然后就记录下Propser的id。
4. Propser第二次带着id和提议去找所有的Acceptor，那些记录的id还是自己的话，Acceptor就接受这个提议，记录下并告诉Learner
5. 当Learner发现这一协议已经超过半数了，那么就代表通过了，然后把信息返回给Client

从执行过程可以看到，朴素Pexos是一个2-Phase，第一阶段先去告诉Acceptor我的id，第二阶段才让Acceptor去接受协议。

> 多Propser的时候要注意
>
> 1. 如果一个Propser携带的id比Acceptor记录的小，那么会被拒绝
> 2. 如果Propser携带的id大于Acceptor的id，那么就覆盖Acceptor的id
> 3. 如果Propser到达的Acceptor已经接受了某个协议，那么Propser就会把自己的协议改成Acceptor记录的协议



#### Multi Paxos

Basic Paxos因为是2-Phase，所以延时很高，而且它最后只确定了一个值。而Multi Paxos正好解决了这两个问题。

{% asset_img 2.png Multi Paxos %}

如图所示，Multi Paxos只有一个阶段，其实在Basic Paxos中第一个阶段只是为了防止多个提议照成的冲突，而真正验证是否一致性的是第二个阶段，当超过一半的都已经接受同一个提议，那么则个协议肯定通过了。

而之所以能去掉第一个阶段是因为只有一个Propser在提议，所以只要保证半数成功即可，因为是一个所以不能并发，而这个Propser的选取有的说是使用Basic Paxos来选举的，其实在Paxos原论文有一种更好的方式，就是Acceptor节点接受到其他节点的请求的话，执行一段时间的提交请求。