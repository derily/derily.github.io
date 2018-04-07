---
title: N层架构
date: 2018-01-29 16:07:56
tags: ABP
---

## 简介
应用程序基于代码结构分层是一个被广泛接受的技巧用爱帮助降低复杂性并且提高代码的重用性。为了达到这一目标，ABP采用基于DDD的原则。

在DDD中有四个基础层
- **呈现层(Presentation Layer)**:提供用户交互的入口，通过`Application Layer`与之交互。
- **应用层(Application Layer)**:协调呈现层和领域层。组织业务逻辑对象来执行应用任务。
- **领域层(Domain Layer)**:包含业务对象和规则，这是应用的核心层。
- **基础设施层(Infrastructure Layer)**:大多数使用第三库为其他层提供通用的技术。

ABP应用架构模型
DDD之外，在现代应用架构中还有其他的逻辑和物理层。下面是ABP推崇并实现的架构模型，ABP不仅提供了基类和服务方便实现此模型同时可以直接使用启动模板(`startup templates`)基于此模型开发。
{% img /images/abp-nlayer-architecture.png "abp-nlayer-architecture" %}

Client Application
通过HTTP APIs(Controller也许是GrphQL endpoint)作为服务来使用应用的远程客户端。远程客户端可以是SPA,APP,或者是第三方消费者。本地化和导航可以在应用本身完成。
