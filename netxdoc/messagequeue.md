## 内存队列
 采用```BlockingCollection```实现的简单内存级别的消息队列

 ### 使用方法

1. 依赖注入
    ```
    //添加消息队列
    services.AddMemoryQueue(p => p.AsScoped(), new Assembly[] { Assembly.GetExecutingAssembly() });
    ```
    > AsScoped ： 为发布者生命周期 <br />
    消费者生命周期为->Transient

2. 消息消费者
    ```
    public class ConsumerHandle : IConsumer<XXXConsumerModel>
    {
        //队列名称
        public string QueueName => "与消息发布者保持一致";

        /// <summary>
        /// 
        /// </summary>
        /// <param name="message"></param>
        /// <returns></returns>
        public async Task Handle(XXXConsumerModel message)
        {

        }
    }
    ```
    > XXXConsumerModel :必须继承自 MessageArgument<br />
    QueueName: 与消息发布者保持一致 <br />
    XXXConsumerModel.GroupName:  <br />
    消息分组聚合根,消息体包含此消息分组信息<br />
    分组为空：消息存放队列为发布的队列名称<br />
    分组不为空：消息存放的队列为 发布的队列名称 + 分组名称.既，不同分组的相同队列名称的数据，将存放与不同的队列

3. 消息发布
    ```
        private readonly IPublisher _publisher;
       
        public XXXXXX(IPublisher publisher)
        {
            _publisher = publisher;
        }

        /// 发布消息
        public async Task Test()
        {
            await _publisher.Publish("QueueName", new XXXConsumerModel());
        }
    ```


## 其他队列（计划中）