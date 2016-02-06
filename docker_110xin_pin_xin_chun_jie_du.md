# Docker 1.10新品新春解读

今天是农历新年到来前的最后一天，数人云谢师傅委托我写一遍对 Docker 1.10发布的使用总结，讲讲它有什么亮点和缺点，未来趋势是什么。我作为数人云的技术背后的“梅长苏”，肯定责无旁贷。假如各位读者对 Docker 技术非常细心关注的话，一定会了解到 Docker 的版本发布是非常平凡的，每次都是说一些“革命性”的特性。好像我们只需要升级就可以了，哪来那么多事情去关心它的细节。那么，这次的发布您就需要记住了，它是一次里程碑式的发布。这次1.10的发布主要着眼点在Compose文件，安全，网络等等特性之上，可以算是 Docker 发布以来最有诚意的版本。

当然，这次升级带来的伤害也是我们用户无法承受之痛。让我们抬起头直面问题，共同解决吧。当然数人云责无旁贷地将会在2016年为企业解决企业上容器的痛苦。

Engine 1.10
-----------
* 由于Content addressable image IDs技术的采用：需要用户在生产环境做自动迁移。如果你的主机上有多个 Image，迁移过程将是漫长的。
* Better event stream：系统事件对于运维2.0来说，增强了更多的事件指标。
* Improved push/pull performance and reliability: 号称3倍以上的速度提升，但对于数人云服务过的客户来说，稳定才是最重要的。
* Live update container resource constraints: 终于有了热升级资源配置的命令。举个例子，当你启动一个32M 大小的容器实例来抢微信红包，由于聊天群火爆，容器实例处理需要更大的内存容量。这个时候，如果能动态更新容器的内存限制就好了。所以，今年过年你可以放心的用容器抢红包了。:-)
* Daemon configuration file: Docker 之前最大的吐槽点就是有 root daemon。这个 Daemon 管理这所有的容器。你想给它加个动态标签，配置个 log 服务器还要重启。这在企业里，重启这个技术活就是一个危险的操作，别说这种容器，是一种高频率的更新配置的需求，总不能让业务天天重启吧。
* Temporary filesystems:临时目录不是真的“临时”，是企业应用过程中，对容器要求只读的时候，可以指定/etc，/tmp，/run 等目录是可以读写的。那么怎么玩的呢？给你一个例子感受下：
```
docker run -d --read-only --tmpfs /run --tmpfs /tmp IMAGE
```
* Constraints on disk I/O: 磁盘 IO 终于可以做限制了。企业特性。
* Splunk logging driver: 全球“最”流行的日志采集分析工具也默认有Docker 去驱动了。
* Start linked containers in correct order when restarting daemon:重启 Daemon 之后，相互依赖的容器可以按照依赖关系重启。
* 

Swarm 1.1
-----------
* Reschedule containers when a node fails: 很多朋友都是问我，数人云和 Swarm的区别，之前我还可以说数人云是可以自动调度容器的，现在 Swarm 也有了这个功能。
* Better node management: 节点管理更轻松了。节点坏了，Swarm 可以显示出来了。


Machine 0.6
-----------
* No need to type “default”: 去掉一个默认参数。
* New provision command: 新增重置环境的命令。


Registry 2.3
----------
其实没有什么更新，就是优化了manifest格式，但是，但是，企业又要重新部署2.3版本的镜像仓库了。和之前版本的不兼容，Docker 是不提供售后服务的。请找数人云吧。





