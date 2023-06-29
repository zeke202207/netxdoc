## 如何实现自己的定时任务

- 新增一个自定义module （关于如何新增module，[请参考](/netxdoc/startup.md))
- 在自定义module中新建一个定时处理类,并集成JobTask、添加JobTask标签

```
[JobTaskAttribute("全局唯一标识","任务描述")]
public class YOURJob : JobTask
{
}
```
完事，结束，就这么简单,开始享受吧 :heart_eyes::heart_eyes::heart_eyes:

<font color='red'>BTW -> </font>在任务中获取注入对象，请使用```MockTenantScope``` 请参考 [tenant章节](/netxdoc/tenands.md)