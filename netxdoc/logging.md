## <a name='s-0'></a>总览🧸 

系统已经默认开启日志记录功能，日志会记录到当前租户的数据库中。日志配置如下

```
{
  "loggingoption": {
    "MinimumLevel": "Trace"
  },
  "loggingmonitor": {
    "withreturnvalue": false, 
    "returnvaluethreshold": 0,
    "jsonbehavior": 1 
  }
}
```

> loggingoption -> MinimumLevel : 配置日志记录最小等级 <br/>
loggingmonitor节点配置：<br/>
withreturnvalue : 日志是否包含返回值结果.<font color='red'>跟踪调试问题开启，建议平时关闭(或配合returnvaluethreshold使用)</font> <br/>
returnvaluethreshold : 返回值结果大于returnvaluethreshold设定的长度将被截断 0：从不截断 ，在withreturnvalue开启时生效 <br/>
jsonbehavior : 日志输出格式  0: 包装文本格式 1 : json格式


## <a name='s-1'>系统日志

[请参考日志管理模块实现](/netxdoc/modules/admintools/logging.md)


## <a name='s-2'>审计日志

[请参考日志管理模块实现](/netxdoc/modules/admintools/logging.md)

## <a name='s-3'>支持自己扩展日志

### 注入自己的日志写入类

```
 public override void ConfigureServices(IServiceCollection services, IWebHostEnvironment env, ModuleContext context)
    {
        //add log
        services.AddDatabaseLogging<DatabaseLoggingWriter>(context.Configuration, option =>
        {
            option.HandleWriteError = error => Console.WriteLine(error.ToString());
        });
        services.AddMonitorLogging(context.Configuration);
    }
```

> DatabaseLoggingWriter : 自己扩展的日志写入提供器

### 日志写入提供器类规范

> 继承 ```ILoggingWriter``` 接口，实现 Write 方法即可

```
public class YourClass : ILoggingWriter
{
    /// <summary>
    /// 按照你的需求记录日志
    /// </summary>
    /// <param name="message">日志内容</param>
    public void Write(LogMessage message)
    {

    }
}
```
