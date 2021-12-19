# raft

[raft英文论文](./raft.pdf)

[raft中文论文](https://github.com/maemual/raft-zh_cn/blob/master/raft-zh_cn.md)

[raft博士论文](./OngaroPhD.pdf)

[raft ppt](http://www2.cs.uh.edu/~paris/6360/PowerPoint/Raft.ppt)

[Raft: A Consensus Algorithm for Replicated Logs](https://www.cs.utah.edu/~stutsman/cs6450/public/raft.pdf)

[Raft lecture (Raft user study)](https://www.bilibili.com/video/BV1564y1i7Wd/?spm_id_from=333.788.recommend_more_video.0)

1. [Raft算法原理](https://www.codedump.info/post/20180921-raft/)
2. [Raft协议精解](https://cloud.tencent.com/developer/article/1185185)
3. [分布式一致性算法Raft](https://cloud.tencent.com/developer/article/1836319)
4. [从Paxos到Raft，分布式一致性算法解析](https://cloud.tencent.com/developer/article/1805939)
5. [超详细教程！手把手带你使用Raft分布式共识性算法](https://cloud.tencent.com/developer/article/1894679)
6. [深入剖析共识性算法 Raft](https://xie.infoq.cn/article/e145b0ce120e0ad77495017d6)
7. [一致性协议浅析：从逻辑时钟到Raft](https://zhuanlan.zhihu.com/p/57109373)
8. [分布式一致性协议概述](https://zhuanlan.zhihu.com/p/130974371)
9. [Raft 一致性协议](https://zhuanlan.zhihu.com/p/29678067)
10. [别再怀疑自己的智商了，Raft协议本来就不好理解](https://zhuanlan.zhihu.com/p/36547283)
11. [易于理解的分布式共识算法，Raft!](https://www.bilibili.com/video/BV1Wy4y1K7zF?from=search&seid=17513827445260524308&spm_id_from=333.337.0.0)
12. [Raft 分布式一致性(共识)算法 论文精读与ETCD源码分析](https://www.bilibili.com/video/BV1CK4y127Lj?from=search&seid=12071754576012314707&spm_id_from=333.337.0.0)
13. [Raft-论文导读与ETCD源码解读](https://hardcore.feishu.cn/docs/doccnMRVFcMWn1zsEYBrbsDf8De)
14. [Raft Visualization](https://raft.github.io/)
15. [The Secret Lives of Data](http://thesecretlivesofdata.com/raft/)
16. [raft-java](https://github.com/wenweihu86/raft-java)
17. [彻底搞懂Raft算法](https://www.bilibili.com/video/BV1Ev411t7jh?from=search&seid=8166262473378527174&spm_id_from=333.337.0.0)
18. [6.824 Lab 2: Raft](https://pdos.csail.mit.edu/6.824/labs/lab-raft.html)

# 笔记
一、Raft协议约定，Candidate在使用新的Term进行选举的时候，Candidate能够被选举为Leader的条件为：

* `得到一半以上(包括自己)节点的投票`
* `得到投票的前提是：Candidate节点的最后一个LogEntry的Term比Follower节点大；或者在Term一样情况下，LogEntry的SN(serial number)必须大于等于Follower的`

二、Raft日志复制原则：
* `只有当前Term的LogEntry提交条件为：满足多数派响应之后(一半以上节点Append LogEntry到日志)设置为Commit`
* `前一轮Term未Commit的LogEntry的Commit依赖于高轮Term LogEntry的Commit`
* `Follower在接收到LogEntry的时候，如果发现发送者节点当前的Term大于等于Follower当前的Term；并且发现相同序号的(相同SN)LogEntry在Follower上存在，未Commit，并且LogEntry Term不一致，那么Follower直接截断从[SN~文件末尾)的所有内容，然后将接收到的LogEntry Append到截断后的文件末尾`

三、PreVote：

在PreVote算法中，Candidate首先要确认自己能赢得集群中大多数节点的投票，这样才会把自己的term增加，然后发起真正的投票。其他投票节点同意发起选举的条件是（同时满足下面两个条件）：

* 没有收到有效领导的心跳，至少有一次选举超时。
* Candidate的日志足够新（Term更大，或者Term相同raft index更大）。


PreVote算法解决了网络分区节点在重新加入时，会中断集群的问题。在PreVote算法中，网络分区节点由于无法获得大部分节点的许可，因此无法增加其Term。然后当它重新加入集群时，它仍然无法递增其Term，因为其他服务器将一直收到来自Leader节点的定期心跳信息。一旦该服务器从领导者接收到心跳，它将返回到Follower状态，Term和Leader一致。

四、黄金铁律：
* 任期大的节点对任期小的拥有绝对的话语权，一旦发现任期大的节点，立马成为其追随者。
* currentTerm、votedFor和log任何一个字段只要发生了更改，立马调用persist函数。
