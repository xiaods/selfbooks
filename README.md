# Apache Mesos与Google Kubernets的不同之处在哪里？
翻译者：肖德时
作者：Craig McLuckie 
Source: https://kismatic.com/community/apaches-mesos-vs-googles-kubernetes/


假如你刚入门集群计算，那么Kubernetes将会是很好的出发点；它快捷，易用，轻量的方式来处理集群导向开发的集成和体验。它提供了非常高瞻远瞩式的可移植性，让它得以支持多家提供商（微软，IBM，红帽，CoreOS，Mesosphere, Kismatic, VMware等等)

Kubernetes是一个开源项目，由Google Cloud Platform组开发，给全球的虚拟机带来了容器集群管理的能力，当然还包括裸机硬件。它能和当代的操作系统很好的一起工作，例如：Ubuntu, RedHat, Project Atomic/CentOS, 或者CoreOS。这些系统提供了轻量的计算节点来受你托管。Kubernetes是用Golang语言写的，它轻量、模块化、可移植还有扩展性。Google的Kubernetes开发组正在和数个不同背景的技术公司一起制定基于Kubernetes作为标准的计算集群的标准方案。这个想法就是借鉴Google在构建分布式应用上积累的经验来帮助需要构建分布式应用的开发者。这些核心的理念包括以下一些基本概念：
* Pods --- 是连接在一起的容器组合并共享文件卷。它们是最小的部署单元，由Kubernetes统一创建、调度、管理。Pods是可以直接创建的，但推荐的做法是你使用replication controller，即使是创建一个Pod。
* Replication controllers --- 管理Pods的生命周期。它们确保指定数量的Pods会一直运行，通过创建和杀掉Pods可以保证到这个效果。
* Labels --- 它被用来管理和选取基于键值对为基础的对象组。
* Services --- 提供独立、可靠名称和地址的Pods集合。它就像一个基础版本的负载均衡器。

所以，伴随着Kubernetes，你将获得一些简单、易用的获得即起即用性，可一致性和扩展性。当加入“分布式”这个术语到你管理的事情上，这个事情将是一个真正轻量的方式。在一个集群里运行应用程序，不再担心单独的主机。在这个例子中，一个集群是一个灵活的资源就像一个虚拟机。它是一个逻辑计算单元，你可以启动它，使用它，调整集群大小，关闭它，既快速也容易。

对于Mesos，这里有很多基本观点的重叠定义，但是产品确实有一些不用的点在他们的生命周期之中，并且有一个亮点。Mesos是一个分布式系统内核，编织不同类型的主机放在一起当一台逻辑计算电脑。它的出现是基于你拥有大量的物理机资源让你能够使用，来创建大型的静态计算集群。很重要的事情是它能让很多现代可扩展的计算处理应用能运行的很好在Mesos集群之上（Hadoop, Kafka和Spark)。它非常棒的地方在于可以在同样的基础资源环境里同时可以运行这些计算处理应用，包括同时运行微服务时代的容器类型的应用。有些地方它确实比Kubernetes重，但是它越来越易用，这要归功于Mesosphere公司的团队贡献。

现在让这个事情更有趣的是Mesos已经开始采用并加入更多Kubernetes的概念来支持Kubernetes API。假如你需要Mesos的特性，这样会是一个网桥连接Mesos，以让你的Kubernetes程序能到更多兼容性的特性（比如：高可靠Master，更多调度的概念，以及管理大量节点的能力）。并且它已经很好的适配到生产环境中（Kubernetes仍然是一个预览版测试阶段，V1的调度将在1到2个月被发布出来）。

假如你已经有了特殊的环境（Spark, Hadoop, Kafka等等），Mesos将给你一个框架，让你插入这些系统到你的集群里，并且可以混合运行一些Kubernetes程序。Mesos给你提供了一个安全阀，在你需要这些计算能力但社区还没有提供实现的情况下可以实现支持。

假如你来自虚拟机的世界，或者传统的服务器架构，Kubernetes将是很好的选则。Mesos需要一个自定义的"Framework"来支持例如运行 MariaDB，但是Kubernetes的容器化应用却可以直接运行，并不需要任何修改。这是一个进入可扩展容器集群的最佳办法。
