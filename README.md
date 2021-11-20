# raft
1. [Raft 一致性协议](https://zhuanlan.zhihu.com/p/29678067)
2. [别再怀疑自己的智商了，Raft协议本来就不好理解](https://zhuanlan.zhihu.com/p/36547283)
3. [易于理解的分布式共识算法，Raft!](https://www.bilibili.com/video/BV1Wy4y1K7zF?from=search&seid=17513827445260524308&spm_id_from=333.337.0.0)
4. [Raft 分布式一致性(共识)算法 论文精读与ETCD源码分析](https://www.bilibili.com/video/BV1CK4y127Lj?from=search&seid=12071754576012314707&spm_id_from=333.337.0.0)
5. [Raft-论文导读与ETCD源码解读](https://hardcore.feishu.cn/docs/doccnMRVFcMWn1zsEYBrbsDf8De)
6. [Raft Visualization](https://raft.github.io/)
7. [The Secret Lives of Data](http://thesecretlivesofdata.com/raft/)
8. [深入剖析共识性算法 Raft](https://xie.infoq.cn/article/e145b0ce120e0ad77495017d6)
9. [raft-java](https://github.com/wenweihu86/raft-java)
10. [彻底搞懂Raft算法](https://www.bilibili.com/video/BV1Ev411t7jh?from=search&seid=8166262473378527174&spm_id_from=333.337.0.0)
11. [6.824 Lab 2: Raft](https://pdos.csail.mit.edu/6.824/labs/lab-raft.html)

# 笔记
一、Raft协议约定，Candidate在使用新的Term进行选举的时候，Candidate能够被选举为Leader的条件为：

* `得到一半以上(包括自己)节点的投票`
* `得到投票的前提是：Candidate节点的最后一个LogEntry的Term比Follower节点大；或者在Term一样情况下，LogEntry的SN(serial number)必须大于等于Follower的`

二、Raft日志复制原则：
* `只有当前Term的LogEntry提交条件为：满足多数派响应之后(一半以上节点Append LogEntry到日志)设置为Commit`
* `前一轮Term未Commit的LogEntry的Commit依赖于高轮Term LogEntry的Commit`
* `Follower在接收到LogEntry的时候，如果发现发送者节点当前的Term大于等于Follower当前的Term；并且发现相同序号的(相同SN)LogEntry在Follower上存在，未Commit，并且LogEntry Term不一致，那么Follower直接截断从[SN~文件末尾)的所有内容，然后将接收到的LogEntry Append到截断后的文件末尾`
