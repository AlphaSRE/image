# Reboot————凤凰涅槃，运维工程师的浴火重生

今天是2015年12月21号，三年前的今天，是玛雅人预言的世界末日，也许世界末日我们是无法亲眼见到了，但运维工程师的世界末日，已经来到。

尽管运维开发已经是被炒了很久的概念，但大部分运维工程师，还处于机房上架，系统装机，环境部署，处理监控报警，配合开发人员的工作等技术含量不高，但是重复劳动较多的工作状态。但是，随着aws，阿里云等各个云供应商的出现，大部分小公司和绝大部分创业公司，都不会自己购买服务器了，本人和一些中小公司里面的朋友了解过，现在大部分工作内容，就是监控、上线。可以预想到的未来，原始运维人员即使不被程序替代，也只剩下点点鼠标，而这样的工作，显然是拿不到高的薪酬。

笔者作为应届毕业生，也是在国内一线互联网公司做运维，做的也是很多重复性的工作，各种开源软件的使用倒是信手拈来，但是，只是部署，搭建，甚至调优的工作都比较少。shell、python也都写了无数的脚本，但还是感觉很low，前一段一直比较迷茫，直到看了前同事雪松大神的[python实例手册](https://github.com/liquanzhou/ops_doc/blob/master/python%E5%AE%9E%E4%BE%8B%E6%89%8B%E5%86%8C.py)，知道了[**Reboot**](http://51reboot.com/),先看下高大上的架构师课表:
> http://51reboot.com/course/arch/

我们要成为DevOps或OpsDev、SRE，就是要比开发人员更懂开发，架构班的课程没有讲太多的python基础，讲的都是高阶语法，比如装饰器，面向对象，多进程多线程，网络编程等等，而且课程内容都是PC老师在BAT工作期间，实际写的程序。例如多进程多线程，很多网络上的blog就是写了几个简单例子，就算完了。但PC老师对进程和线程独到的理解，对linux底层实现的具体分析，都使我们的理解更加深入，运用的更加得心应手，所谓知其然，亦知其所以然。

更重要的是PC老师在课程中穿插的各种原理，例如TCP/IP协议，分布式系统架构，CAP原理，分布式一致性算法Paxos，linux底层接口调用等等，用PC老师的话，这些东西，是十年之内都有用的。

下面是我们完成的项目架构图：
![arch](http://upload-images.jianshu.io/upload_images/1362367-140b7bc66ece754f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这套系统的核心是我们使用python实现了memcache原型状态机的异步网络框架，通过状态机结合epoll，分分钟秒杀现有python开源框架。下面是笔者的测试，单线程可以达到3W+QPS，
测试程序：

- server端[nbNetFramework.py](https://github.com/51reboot/rebootMon/blob/master/nbNet/nbNetFramework.py)
- client端[loadrun.py](https://github.com/51reboot/rebootMon/blob/master/nbNet/loadrun.py)

启动方法：

        python nbNetFramework.py
        python loadrun.py a 100 

每10W个请求print下时间：

![qps1](http://upload-images.jianshu.io/upload_images/1362367-9f1282f3af67222a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![qps2](http://upload-images.jianshu.io/upload_images/1362367-6f07255268acc9de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

班里有个阿里系的某大神同学，使用逆天的pypy，达到了50W+QPS：

![qps3](http://upload-images.jianshu.io/upload_images/1362367-28e586cd21722dfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![qps4](http://upload-images.jianshu.io/upload_images/1362367-c598a191e3c28c95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![qps5](http://upload-images.jianshu.io/upload_images/1362367-0a17dc9505faee2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

虽然只是测试，处理逻辑稍显简单，但依然很强大，单台监控数万服务器，性能绝对不成问题，源码已经在github开源：

> https://github.com/51reboot/rebootMon

源码包括了整个的架构代码，和前端的展示。

这套系统，我们后期完善了下远程rpc命令执行调用的异步处理，所以也可以作为批量任务执行系统，网络框架和逻辑几乎完全剥离，因此可以作为通用的C/S框架，只需写logic代码。