---
title: ABP中文文档
date: 2018-01-29 13:17:58
tags: ABP
---

> 本篇是ABP中文文档系列的第一篇，之所以开始翻译ABP文档一是提高自己对于框架本身的理解，二是方便大家不用啃英文，快速对框架有一个基本的认识，当然翻译再所难免有理解偏差，有不同见解的同学可以在文章后面评论，若大家都认同我会在后面对其进行更正，谢谢大家，下面开始主题。

## 介绍
不同的应用都是基于不同的需求进行创建的。在某种程度上，我们会反复的实现类似功能模块，比喻授权、验证、异常处理、日志、本地化、数据库连接管理、设置管理和审计日志这些公共的模块。我们总是在构建体系结构和最佳实践比喻分层和模块化架构，领域驱动设计，依赖注入和其他等等，也会基于约定来开发这些应用。

然而所有这些都是耗时并且很难为每一个项目分离构建，很多公司创建自己的私有框架。使用这些框架，他们可以更高效并且较少bug。但是不是所有公司都如此lucky。大多数没有时间和预算和团队去开发这样的框架。即使他们有这样的能力做这件事，也很难形成规范的文档，方便其他开发者快速学习，还要去维护迭代。

ABP是一个开源并且拥有良好文档的应用程序框架，初心是开发一个所有公司和开发者都能获益的公共框架，然而他又不仅仅是一个框架，同时也提供一个健壮的基于领域驱动设计的架构模型和很多最佳实践。

## 一个简单的实例
让我们浏览一个类看看ABP带来的益处
``` cs
public class TaskAppService:ApplicationService,ITaskAppService
{
    private readonly IRepository<Task> _taskRepository;
    public TaskAppService(IRepository<Task> taskRepository)
    {
        _taskRepository=taskRepository;
    }
    [AbpAuthorize(MyPermissions.UpdateTasks)]
    public async Task UpdateTask(UpdateTaskInput input)
    {
        Logger.Info("updateing a task for input:" + input);
        var task=await _taskRepository.FirstOrDefaultAsync(input.TaskId);
        if(task==null)
        {
            throw new UserFriendlyException(L("CouldNotFindTheTaskMessage"));
        }
        input.MapTo(task);
    }
}
```
这是一个简单的`application Service`方法。在DDD中`application service`是直接被呈现层作为应用用例执行。本例中的`UpdateTask`将被前端js通过AJAX调用

让我们看看ABP拥有的特性或者是益处：
- Dependency Injection:ABP使用并且提供一个强大的基于约定的DI基础设施。如果一个类是`application service`,按照约定会作为短暂对象（每个请求重新创建）自动注册到DI容器中。可以简单的注入任何依赖，比喻此实例中的`IRepository<Task>`。
- Repository:ABP为每个实体(entity)创建默认的仓储，此实例中`IRepository<Task>`,其中`Task`为实体对象。默认的仓储有许多有用的方法如`FirstOrDefault`。可以很容易根据自己的需求扩展默认仓储。仓储抽象关系型数据库与ORMs框架简化数据访问逻辑。
- Authorization:ABP可以检查权限。如果当前用户没有`update tasks`权限或者没有登录，将被阻止访问`UpdateTask`方法。ABP不仅可以使用声明特性，同时也提供其他的方式让你使用授权特性。
- Validation:ABP自动检查输入是否为空对象。他可以基于`data annotation`特性和个性化验证规则验证所有输入属性的合法性。假如请求不合法，他将抛出合适的验证异常。
- AuditLogging:基于配置和约定用户、浏览器、IP地址、调用服务方法以及参数、调用时间、执行时长等等这些信息会自动保存。
- Unit Of Work:在ABP中每个`application service`方法默认都被假定为一个unit of work，他会自动创建一个连接同时在方法的开始创建事务。如果方法执行没有异常抛出成功完成，事务将在连接释放时提交，即使方法中包含不同的仓储或者其他的方法，所有这些都是原子的。所有对实体对象的变更都将在事务提交后保存，连`_repository.Update(task)`方法都不用调用。
- Exception Handling:web应用程序中我们都不用手动处理异常。所有异常默认都被自动处理，ABP自动记录他并且返回一个合适的结果给客户端。举例一个ajax请求，他返回一个json对象给客户端表示错误发生。如果异常是个用户友好异常将对客户端隐藏真实的异常，就像本实例中处理task==null的情况。
- Logging:正如你所看到的我们在实例中使用Logger对象。默认使用Log4Net日志组件，当然你也可以更换或者配置它。
- Localization:不知道你注意到我们抛出用户友好异常时使用`L`方法，这个方法根据用户当前的culture自动实现本地化，我们在其他地方定义`CouldNotFindTheTaskMessage`内容。
- Auto Mapping:本实例中最后一行我们使用了MapTo扩展方法映射输入属性到实体属性。使用了AutoMapper库执行映射。我们可以很容易根据命名约定映射属性。
- Dynamic Web API Layer:TaskAppService 是一个简单的类，现实中我们一般还要包装将其包装成一个WebAPI控制器对javascript客户端提供方法调用，然而ABP框架已经在运行时自动为你做了。
- Dynamic Javascript AJAX Proxy:ABP创建了代理方法让你在客户端想调用javascript方法一样调用应用服务。

## 其他的特性
+ Modularity:提供了一个强大的基础设施构建可重用的模块。
+ Data Filters:自动数据过滤实现了一些模式如软删除和多租户。
+ Multi Tenancy:完全支持多租户，包括单个数据库或者每个租户一个数据库的结构。
+ Setting Management:提供了一个强大的基础设施更改和获取应用，租户和用户级别的设置。
+ Unit & Integration Testing:ABP也是考虑到可测试性构建的。它提供了一些基础类来简化单元和整合测试。

下一篇：{% post_link N层架构 %}
