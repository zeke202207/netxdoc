
## <a name='s-1'></a>事件总线用法

 > 例：Module2 进行事件发布，需要在Moudle1 （MoudleN） 中进行订阅，进行事件处理

## <a name='s-2'></a>发布处理
- 注入事件总线
    ```
        private readonly IEventPublisher _publisher;
        public Test2Controller(IEventPublisher publisher)
        {
            _publisher = publisher;
        }

        public async Task Test()
        {
            await _publisher.PublishAsync(new EventSource("zeke","hi,zeke"),new CancellationToken());
        }
    ```

## <a name='s-3'></a>订阅处理

- 注入订阅程序
    
    ```
        public override void ConfigureServices(IServiceCollection services, IWebHostEnvironment env, ModuleContext context)
        {
            services.AddScoped<IEventSubscriber, EventHandler>();
        }
    ```

- 订阅处理程序

    ```
        public class EventHandler : IEventSubscriber
        {
            [EventSubscribe("zeke")]
            public async Task Handler(EventHandlerExecutingContext context)
            {
                Console.WriteLine("ok");
                await Task.CompletedTask;
            }
        }
    ```

## <a name='s-4'></a> 注意事项

   * 事件发布的```EventSource```中的```EventId```一定与订阅处理```EventSubscribe```特性中的```EventId```一致
   * 事件订阅者，一定药实现```IEventSubscriber```这个接口,并在其处理方法上方添加```[EventSubscribe]```特性
   * 支持一个事件，多个订阅模式(EventSourceId保持能对应上即可)
