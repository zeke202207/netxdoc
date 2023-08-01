## 介绍

> 框架内置了责任链变体模式：ChainDataflow 和 PipelineDataflow两种模式。<br />
ChainDataflow: 支持输入与输出的管道数据处理<br />
PipelineDataflow：支持仅输入无输出的管道数据处理<br />

## 使用方式

管道模式提供netcore通过micsoft注入方式和正常的非注入2中使用方法

### 注入方式

```
    //注入代码
    public override void ConfigureServices(IServiceCollection services, IWebHostEnvironment env, ModuleContext context)
    {
        services.AddChain<DataflowParameterA, DataflowResultA>(ServiceLifetime.Transient,typeof(ChainMiddlewareA), typeof(ChainMiddlewareB), typeof(ChainMiddlewareC));
    }

    //使用方式    
    private readonly IChain<DataflowParameterA, DataflowResultA> _chain;
    public TestController(IChain<DataflowParameterA, DataflowResultA> chain)
    {
        _chain = chain;
    }
    
    public IEnumerable<string> Get()
    {
        var result = _chain.Execute(new DataflowParameterA() { Id = "zeke" });
        return new List<string>();
    }

```
> DataflowParameterA: 入参类型<br />
DataflowResultA: 返回值类型<br />
ChainMiddlewareA、ChainMiddlewareB、ChainMiddlewareC 管道中间件（既管道的3个处理阀门）

### 非注入方式

```
    var chain = new Chain<DataflowParameterA, DataflowResultA>(new ActivatorMiddlewareCreater());
    chain.Add<ChainMiddlewareA>();
    chain.Add<ChainMiddlewareB>();
    chain.Add<ChainMiddlewareC>();
    var result = chain.Execute(new DataflowParameterA() { Id = "zeke" });
```

## Chain管道模式（有返回值）

使用如下方法
- AddChain
- AddChainAync

```
services.AddChain<ParameterX, ResultX>(...)
services.AddChainAync<ParameterX, ResultX>(...)
```

## Pipeline管道模式（没有返回值）

使用如下方法
- AddPipeline
- AddPipelineAsync

```
services.AddPipeline<ParameterX>(...)
services.AddPipelineAync<ParameterX>(...)
```

## 管道阀门执行顺序设定

使用如下特性标记实行顺序
- ChainPipeline

```
    [ChainPipeline(0)]
    public class ChainMiddlewareB : IChainMiddleware<DataflowParameterA, DataflowResultA>
    {
    }
```

## 管道阀门处理取消操作
---
1. 在阀门处理过程中，入参```RequestContext<ParameterX>``` 使用方法 ```parameter.Cts.Cancel();```进行中断
2. ```chain```模式下，异常被包装在了返回结果中<br />
```pipeline```模式下，异常未处理，由上层应用捕获处理,取消后操作后，将抛出系统异常
