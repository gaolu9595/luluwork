# Inspirations

[TOC]

## Development and Deliver of Avatar Engine 

1. 设计阶段：
   1. 弄明白project背景和要解决的问题
   2. 梳理project的整个pipeline和主要业务逻辑
   3. 根据业务逻辑，从宏观上搭建一个project，定义好大部分重要的接口

2. 开发阶段：

   1. 在使用一个库、一项技术或者一个框架之前，要做比较详尽的调研，要知其然更要知其所以然
   2. 了解当前编程语言的编译和运行机制，要知道怎么让一个程序跑起来
   3. application的启动：怎么用命令启动，怎么指定和读取配置文件
   4. 配置文件很重要，配置文件书写规范
   5. 写代码的时候以考虑写出能复用的代码为基本准则，不断对代码结构做优化
   6. 在做解决方案时，首先要多找找已有的good practice，其次是必须要结合实际业务场景，如dedup方案
   7. 出现bug很正常，要相信慢慢改总能改好的！

3. 测试阶段：

   1. 搭建一套application的运行环境（如kafka、mysql、clickhouse之类的）

      1. 可以在本机搭一套本地运行环境

      2. 可以使用Docker这类容器化引擎来搭建一套和线上一致的虚拟运行环境

         （进一步可以用k8s进行docker容器的管理）

   2. 构建详尽的单元测试，依赖于mock（只需要测自己实际的代码逻辑）

   3. 持续集成（使用Jenkins这一类的工具），将重复性编译打包测试等流程自动化，对编译和自动化测试流做验证

4. 部署阶段（主要由ops参与合作）：

   1. 我们需要对application编译、压缩打包（交叉编译）
   2. application的运行环境、进程管理、配置文件管理、log管理一般由ops提供
   3. 构建完整的监控体系
      1. 指标性监控metrics，一般通过时序数据库来存储，如Grafana等
      2. 日志型监控log，日志的收集、筛选、存储和检索，有很成熟的框架，如ELK等

5. 版本管理

   1. github是主要工具
   2. app包的版本管理可以利用git tag




## Deploy Concepts

refs：

https://docs.microsoft.com/en-us/dotnet/architecture/cloud-native/definition



### CICD 持续集成 持续交付 持续部署

1. CI持续集成：构建出一套自动化的pipeline，在application引入新的feature/fix时自动化构建应用并且运行不同级别的QA验证，如单元测试、集成测试和回归测试等
2. CD持续交付：CI完成后，将已验证的代码包发布到存储库供后续部署
3. CD持续部署：自动将代码包从存储库发布到生产环境

【前期投入大，后期需要维护】



### Container 容器化

服务即容器

隔离性

资源消耗低于VM技术

方便构建CICD的运行环境

方便和prod上的运行环境保持一致，消除在每台机器上独立配置相同环境的开销



### Microservice and Monolith 单体与微服务

单体：随着功能的扩大，monolith fear将会越来越强烈，对整个单体服务感到失控

微服务：

1. 每个微服务具有自己的独立生命周期，可以独立进行开发部署，不必一起release，避免整个服务shutdown
2. 每个微服务的功能更改影响整个服务的risk会降低
3. 每个微服务可以依据其计算任务、网络带宽等，独立进行伸缩扩展



### Cloud Service 云服务

IaaS：IaaS 包含云 IT 的基本构建块。它通常提供对网络功能、计算机（虚拟或专用硬件）和数据存储空间的访问。IaaS 为您提供最高级别的灵活性，并使您可以对  IT 资源进行管理控制。它与许多 IT 部门和开发人员熟悉的现有 IT 资源最为相似。 

PaaS：PaaS 让您无需管理底层基础设施（一般是硬件和操作系统），从而可以将更多精力放在应用程序的部署和管理上面。这有助于提高效率，因为您不用操心资源购置、容量规划、软件维护、补丁安装或与应用程序运行有关的任何无差别的繁重工作。 

SaaS：SaaS 提供了一种完善的产品，其运行和管理皆由服务提供商负责。在大多数情况下，人们所说的 SaaS 指的是最终用户应用程序（如基于 Web 的电子邮件）。使用 SaaS 产品，您无需考虑如何维护服务或管理基础设施。您只需要考虑如何使用该特定软件。 



### Cloud-Native 云原生

指那些在设计开发之初，就为迎合“云”特性而构建的应用。原生的为云而生的应用。

松耦合、可监控、可管理、可伸缩

Offical Definition：

> *Cloud-native technologies empower organizations to build and run scalable applications in modern, dynamic environments such as public, private, and hybrid clouds. Containers, service meshes, microservices, immutable infrastructure, and declarative APIs exemplify this approach.*

> *These techniques enable loosely coupled systems that are resilient, manageable, and observable. Combined with robust automation, they allow engineers to make high-impact changes frequently and predictably with minimal toil.*



