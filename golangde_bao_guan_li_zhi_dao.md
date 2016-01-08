#Golang的包管理之道

对于一门编程语言的开发者，类库包管理是一项考核编程语言成熟度的重要指标之一，Golang 也不例外。笔者在日常使用 Golang语言开发系统程序时发现，在 Golang 的世界里，存在这大量的讨论和各种解决方案。这对 Golang 开发者来说，官方并没有强制大家使用同一个约定方案，这给开发者带来了不少的困惑。所以本文的目的就是想和大家一起，针对这个看似很小的问题之上，一起探讨包管理问题出现的原因以及解决办法，在详细的对比探讨之后，间接地体会出 Golang 语言的开发团队对语言设计的深层设计哲学。

![](https://nathany.com/images/gopher-tagging.jpg)

##Go包管理的现状和问题
 
目前主流的编程语言 Python、Ruby、Java、Php 等已经把包管理的流程设计的犹如行云流水般流畅，一般情况下开发者是不需要操心类库包依赖管理以及升级、备份、团队协作的。在 Golang 的世界里，尤其是在1.5之前，此类库包管理的流程设计真的是“仅仅”能工作的状态。笔者结合日常开发过程中遇到的问题，整理出 Golang 语言包管理的现状如下：

1.  网络环境是一个瓶颈，尤其是遇到大量的依赖包下载时，下载过程就是让开发者长时间等待的过程，直至无法忍受。此类问题困扰多了，国内的开发者做了一个异步[下载 Golang 包的镜像服务](http://golangtc.com/packages)来尝试解决它。但在日常工作中，这种间接的办法并不能有效的解决此类问题。
2. Golang 的第三方包是没有中央库统一管理的，所以不存在索引库的概念。遇到需要的库，一定要小心的检查包的可用性。因为包管理并没有全局的版本控制。当你在本地编译成功之后分享给同事时并不能保障你的同事就能一次编译成功。类库版本不对的情况时常发生，以至于开发者不得不把依赖包直接加到应用代码仓库中。类库小的几十兆，大的上百兆，从开发者的角度来说，代码干净程度是决定一个程序是否优雅和品位的，但是加入例如几百兆的依赖包实在是无奈之举，此方法冰没解决问题。实际上理想中的包管理设计应该是可以自动应对包的依赖管理的，例如 python 的 pip，ruby 的 Bundler。
3. Golang 作为云计算时代最流行的系统级变成语言，目前在全球开发者社区都受到热烈的关注和大量的使用。业界不乏开发者推出自己的包管理解决办法，混乱的包管理治理工作对于开发者来说，耗费了大量无意义的工作。Golang 的开发组也是迟迟没有给出统一的解决办法。

当然，目前 Golang 到了1.5版本时代，官方开始引入包管理的设计，加了 vendor目录来支持本地包管理依赖。这个方法目前还不是默认开放的，goimports 并不能直接使用。官方会在1.6版本开始正式启用这个特性。为了在1.5环境下启用这个特性，Golang 启用了一个环境变量作为开关：**GO15VENDOREXPERIMENT=1** ，1.6之后就会默认启用不再使用此环境变量。

##原因分析
笔者认为，Golang 语言的设计者都是多年经验的世界级语言开发者，发明它也是为了谷歌内部替代 C++/C 的系统级语言，不可能没有考虑包管理。所以 Golang 对包管理一定有自己的理解。笔者从一开始接触Golang 时就发现，它真的引入的新的语言概念非常少。对于包的获取，就是用 go get命令从远程代码库(GitHub, Bitbucket, Google Code, Launchpad)拉取。这样做的好处是，直接跳过了包管理中央库的的约束，让代码的拉取直接基于版本控制库，大家的协作管理都是基于这个版本依赖库来互动。细体会下，发现这种设计的好处是去掉冗余，直接复用最基本的代码基础设施。Golang 这么干很大程度上减轻了开发者对包管理的复杂概念的理解负担，设计的很巧妙。

当然，go tools 引入的 go get 命令，仍然过于简单。对于现实过程中的开发者来说，仍然有其痛苦的地方。

1. 缺乏明确显示的版本。团队开发容易导入不一样的版本
2. 第三方包没有内容安全审计，很容易引入代码 Bug
3. 依赖的完整性无法校验，程序编译时无法保障百分百成功

Go开发组对于此类问题的建议是把外部依赖的代码复制到你的[源码库中管理](https://golang.org/doc/faq#get_version)。

包管理的问题，并不是一个单点问题。它涉及到程序的工程操作性。开发者需要的是可以在任何时间，任何地点和环境，可以反复的编译出同样的程序：[ReproducibleBuild](http://martinfowler.com/bliki/ReproducibleBuild.html)

* 可以在特定的分支上重现一个 Bug
* 使用 **bisect** 可以隔离出哪一次提交引入的 Bug

所以，官方推荐把第三方代码引入自己的代码库仍然是一种折中的办法：

* 对于之前的 go get。我们如何升级依赖库的版本。仍然需要第三方工具或者脚本来维护类库，本身就是有点复杂。
* 我们很难直接针对第三方库的 Bug，贡献代码修复 Bug。所以，你复制的那一份代码已经开始工作后，谁还敢动呢？更糟糕的是，如果这个第三方库的开发者很活跃，代码更新更快，如何升级我们的引用代码呢？
* 官方的办法对于普通的程序问题不是很大，最多就是编译时的依赖。但如果你写的是一个给其他人使用的类库，引入这个库就会带来麻烦了。你这个库被多人引用，如何管理你这个库的代码依赖呢？难道还是一股脑的复制吗？

##几种解法，利弊

由于官方对于包管理暂时没有明确的指导意见，所以，作为社区驱动的一门语言，不缺乏各路优秀开发者推出的自己的最佳实践工具：

* https://github.com/tools/godep
* https://github.com/gpmgo/gopm
* https://github.com/pote/gpm
* https://github.com/nitrous-io/goop
* https://github.com/alouche/rodent
* https://github.com/jingweno/nut
* https://github.com/niemeyer/gopkg
* https://github.com/mjibson/party
* https://github.com/kardianos/vendor
* https://github.com/kisielk/vendorize
* https://github.com/mattn/gom
* https://github.com/dkulchenko/bunch
* https://github.com/skelterjohn/wgo
* https://github.com/Masterminds/glide
* https://github.com/robfig/glock
* https://bitbucket.org/vegansk/gobs
* https://launchpad.net/godeps
* https://github.com/d2fn/gopack
* https://github.com/laher/gopin
* https://github.com/LyricalSecurity/gigo
* https://github.com/VividCortex/johnny-deps

到了1.5后Golang的Vendor目录特性出来后，官方 Wiki 推荐了支持此特性的包管理工具如下：

* Godep
* Govendor
* godm
* vexp
* gv
* gvt - Recursively retrieve and vendor packages.
* govend
* Glide

根据笔者的实践总结下来，对于国外的开发者，因为没有“国家防火墙”的限制，带宽也会非常充足。我推荐使用的工具是 Glide，推荐原因是设计简洁，符合 Golang 的一贯风格。

给一个glide 的配置文件例子参考：
```
 1 package: main                                                                                                                  
  2 import:                                                                        
  3   - package: github.com/coreos/go-etcd                                         
  4     ref:     cc90c7b091275e606ad0ca7102a23fb2072f3f5e                          
  5     subpackages:                                                               
  6       - etcd                                                                   
  7   - package: github.com/docker/distribution                                    
  8     ref:     9038e48c3b982f8e82281ea486f078a73731ac4e                          
  9   - package: github.com/mailgun/log                                            
 10     ref:     44874009257d4d47ba9806f1b7f72a32a015e4d8                          
 11   - package: github.com/mailgun/oxy                                            
 12     ref:     547c334d658398c05b346c0b79d8f47ba2e1473b                          
 13     subpackages:                                                               
 14       - cbreaker                                                               
 15       - forward                                                                
 16       - memmetrics                                                             
 17       - roundrobin                                                             
 18       - utils                                                                  
 19   - package: github.com/hashicorp/consul                                       
 20     ref:     de080672fee9e6104572eeea89eccdca135bb918                          
 21     subpackages:                                         
```

对于国内开发者来说，最好是能一个一个包来管理。遇到网络问题，可以通过国内镜像下载。在这样的情况之下gvt 就是一个不错的选择。它可以帮助我们把一个包以及依赖都彻底的拉到本地的代码库中，统一了团队协作过程中编译环境不一致的问题。

给一个例子参考：
```
$ gvt fetch github.com/fatih/color
2015/09/05 02:38:06 fetching recursive dependency github.com/mattn/go-isatty
2015/09/05 02:38:07 fetching recursive dependency github.com/shiena/ansicolor

$ tree -d
.
└── vendor
    └── github.com
        ├── fatih
        │   └── color
        ├── mattn
        │   └── go-isatty
        └── shiena
            └── ansicolor
                └── ansicolor

9 directories

$ cat > main.go
package main
import "github.com/fatih/color"
func main() {
    color.Red("Hello, world!")
}

$ export GO15VENDOREXPERIMENT=1
$ go build .
$ ./hello
Hello, world!

$ git add main.go vendor/ && git commit
```
##未来
Golang 社区一直遵循“尽量简单”的原则，从不多加一份可能的设计负担给用户，这也是我喜欢它的原因。对于管理依赖的处理，是 Go开发组 一直重视的技术点，它的重要性远比“DRY”原则还过之：

“Through the design of the standard library, great effort was spent on controlling dependencies. It can be better to copy a little code than to pull in a big library for one function. Dependency hygiene trumps code reuse.” - Go at Google
Go Team 强调的是代码的干净度胜过代码的重用。这是不一样的编程哲学，还请大家且行且珍惜。

总结下官方对包管理依赖的建议如下：

* 当你开源类库时，请尽量的少用第三方库，学会使用标准库。发布的类库，也请使用版本服务，类如[gopkg.in](http://labix.org/gopkg.in)来管理版本。
* 对于程序的包管理，使用官方[推荐的工具](https://code.google.com/p/go-wiki/wiki/PackageManagementTools)来管理。如果你有自己的想法，请直接对这些官方推荐的工具做贡献，让社区一起来共同解决这个问题。

###作者:
肖德时，北京数人科技有限公司CTO，负责云计算的研发及架构设计工作。关注领域包括Docker，Mesos集群， 云计算等领域。 肖德时之前为红帽Engineering Service部门内部工具组Team Leader。


参考：
* https://nathany.com/go-packages/
* [Manage Dependencies With Godep](http://www.goinggo.net/2013/10/manage-dependencies-with-godep.html)
* [Go 1.5 Vendor Experiment](https://docs.google.com/document/d/1Bz5-UB7g2uPBdOx-rw5t9MxJwkfpx90cqG9AFL0JAYo/edit)
* https://nathany.com/go-packages/





