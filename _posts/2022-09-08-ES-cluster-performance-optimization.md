---
layout: post
title: ElasticSearch 6亿文档存储的ES集群调优实战
categories: [ElasticSearch, 集群, 大数据]
description: ES集群调优
keywords: ElasticSearch, 集群, 大数据
---

一、问题描述
1. 集群节点3台，配置4核16G
2. 分片副本数5 * 2
3. JVM参数：-Xms4G -Xmx4G
4. 老年代一直涨，GC回收不了
5. 业务数据有批量写入的操作

![](https://img-blog.csdnimg.cn/c9627a66d2754e70b73d90342816cc59.png)
![](https://img-blog.csdnimg.cn/babbbdcbaaef425b8fa4715bb0234293.png)
![](https://img-blog.csdnimg.cn/4feba65077dc43ee90aa307e8387eccc.png)
![](https://img-blog.csdnimg.cn/20ce0132570a4990857ddcbf14eafd5f.png)

```
curl -s 'localhost:9200/_cat/nodes?h=name,fm,fcm,sm,qcm,im&v'
```

fielddata.memory_size (fm), // 字段缓存占用内存
filter_cache.memory_size (fcm) // 过滤语句缓存占用内存
segments.memory (sm) // 每个分片包含的断 占用内存

http://localhost:9200/_nodes/hot_threads

![](https://img-blog.csdnimg.cn/9ffe6641b4944478a4921abbf8b7ed13.png)

二、问题分析
1. 堆内存太小

2. 分片数量不是3的倍数，导致集群压力不均衡

3. bulk批量写入过大

三、解决方案
1. 增大JVM配置参数-Xms8G -Xmx8G

## 参考链接



- [Bulk异常引发的Elasticsearch内存泄漏_wx60e27c825fe44的技术博客_51CTO博客][1]

[1]: https://blog.51cto.com/u_15293891/2995198/

- [es进程占用内存持续增加，快爆了][2]

[2]: https://elasticsearch.cn/question/12490

- [Java启动参数详解_啊荻～的博客-CSDN博客_java启动参数][3]

[3]: https://blog.csdn.net/qq_16268979/article/details/118741867
- [ES 性能调优，这可能是全网最详细的 Elasticsearch 性能调优指南_Elastic开源社区的博客-CSDN博客_es性能调优][4]

[4]:https://blog.csdn.net/wlei0618/article/details/124104738
- [ES内存持续增长问题分析_道友，且慢的博客-CSDN博客][5]

[5]:https://blog.csdn.net/qqqq0199181/article/details/103421680>

- [elasticsearch实际总结(4)—— 查询缓存 - SegmentFault 思否][6]

[6]:<https://segmentfault.com/a/1190000040238635?sort=newest>

- [Elasticsearch内存溢出怎么排查？收藏这篇内存组成！ - 墨天轮][7]

[7]:<https://www.modb.pro/db/388574>

- [Day19 ES内存那点事 - Elastic 中文社区][8]

[8]:<https://elasticsearch.cn/article/32>

- [Arthas性能排查系列（三）火焰图分析_ADAMs.的博客-CSDN博客_arthas火焰图分析][9]

[9]:<https://blog.csdn.net/qq_43097201/article/details/125683217>
- [Elasticsearch 之 Translog][10]

[10]:<https://www.jianshu.com/p/a9da2d74d926>

- [elasticsearch内存占用详细分析_水的精神的博客-CSDN博客_elasticsearch占用大量内存][11]

[11]:<https://blog.csdn.net/star1210644725/article/details/123767566>
- [Elasticesearch内存详解（三）——Segments Memory-阿里云开发者社区][12]

[12]:<https://developer.aliyun.com/article/947904>

- [https://doc.codingdict.com/elasticsearch/][13]

[13]: <https://doc.codingdict.com/elasticsearch/>

