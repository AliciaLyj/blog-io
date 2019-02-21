---
title: shuffle流程浅析
date: 2017-02-21 12:39:04
tags:
---

`Shuffle`过程，也称Copy阶段。reduce task从各个map task上远程拷贝一片数据，并针对某一片数据，如果其大小超过一定的阀值，则写到磁盘上，否则直接放到内存中。

官方的Shuffle过程如上图所示，不过细节有错乱，官方图并没有说明partition、sort和combiner具体作用于哪个阶段。
注意：Shuffle过程是贯穿于map和reduce两个过程的！

Hadoop的集群环境，大部分的map task和reduce task是执行在不同的节点上的，那么reduce就要取map的输出结果。那么集群中运行多个Job时，task的正常执行会对集群内部的网络资源消耗严重。虽说这种消耗是正常的，是不可避免的，但是，我们可以采取措施尽可能的减少不必要的网络资源消耗。另一方面，每个节点的内部，相比于内存，磁盘IO对Job完成时间的影响相当的大，。