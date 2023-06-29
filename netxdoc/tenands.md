## <a name='s-3'></a>使用内置的Tenant、Strage、Store

```
 services.AddTenancy(config).Build();
...
 app.UseMultiTenancy();
```
> 获取当前请求的租户信息： ``` TenantContext.CurrentTenant.Principal ``` 

> 模拟scope过程：
```
 _serviceProvider.MockTenantScope((scope, tenant) =>
            {
                var jobtaskCommand = scope.ServiceProvider.GetService<ICommandBus>();
                //1. 从数据库获取所有任务
                IQueryBus jobtaskQuery = scope.ServiceProvider.GetRequiredService<IQueryBus>();
                var jobs = jobtaskQuery.Send<JobTaskQueryAll, IEnumerable<JobTaskModel>>(new JobTaskQueryAll(string.Empty)).GetAwaiter().GetResult();
                ....
            });
```

###  <a name='-1'></a>扩展租户支持

#### <a name='Tenant'></a>自定义Tenant类扩展
    
```
    public class AppTenant : Tenant
    {
        public string YourProperty{get;set;}
    }
```
#### <a name='-1'></a>自定义租户策略

```
public class YourResolutionStrategy : ITenantResolutionStrategy
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    /// <summary>
    /// 《示例》：构造函数
    /// </summary>
    /// <param name="httpContextAccessor"></param>
    public YourResolutionStrategy(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    /// <summary>
    /// 解析租户身份 
    /// </summary>
    /// <returns>租户身份标识</returns>
    public async Task<string> GetTenantIdentifierAsync()
    {
        //TODO: 您的解析策略
    }
}
```

#### <a name='-1'></a>自定义租户存储

```
public class YourTenantStore : ITenantStore<Tenant>
{
    /// <summary>
    /// 根据租户身份获取租户信息
    /// </summary>
    /// <param name="identifier">租户身份</param>
    /// <returns></returns>
    public async Task<Tenant> GetTenantAsync(string identifier)
    {
        //TODO:根据租户身份标识，获取租户信息
    }
}
```

## <a name='s-4'></a>配置文件对多租户的支持 (推荐使用)

```
{
  "databaseinfo": {
    "databasehost": "127.0.0.1",
    "databasename": "netx-zeke",
    "databaseport": "8306",
    "databasetype": "0",
    "userid": "root",
    "password": "l/y+KD4Pa5Gj8jjloR5WyQ=="
  },
  "tenantconfig": {
    "tenanttype": 0, //0:single,1:multi,
    "resolutionstrategy": "NetX.Tenants.DomainsParse",
    "storestrategy": "NetX.Tenants.InMemoryTenantStore",
    "tenants": [
      {
        // tenantid:要求唯一，多租户下生效，数据库命名格式： tenantid-databasename
        "tenantid": "1",
        //用于解析判定属于哪一个租户
        "identifier": "localhost"
      },
      {
        "tenantid": "2",
        "identifier": "zeke1"
      }
    ]
  }
}
```
> tenanttype ：租户类型的枚举 ： 单租户系统、多租户系统<br/>
resolutionstrategy: 租户信息解析策略<br/>
storestrategy： 租户信息存储策略<br/>
tenants: 租户列表,```tenanttype```为多租户系统时生效<br/>
password: 加密后的数据库连接密码,采用加密算法为``` DES ```


## <a name='-1'></a>数据库对多租户的支持

### <a name='Margration'></a>数据库迁移Margration

- 生成一个独立的数据库迁移模块(参考[用户如何自定义模块](#-1))，并配置其 Initializer 类

    ```
    public class ModuleInitializerDatabaseSetup : ModuleInitializer
    {
        public override Guid Key => new Guid("10000000000000000000000000000001");

        public override ModuleType ModuleType => ModuleType.UserModule;

        public override void ConfigureApplication(IApplicationBuilder app, IWebHostEnvironment env, ModuleContext context)
        {
            
        }

        public override void ConfigureServices(IServiceCollection services, IWebHostEnvironment env, ModuleContext context)
        {
            services.AddMigratorAssembly(new Assembly[] { Assembly.GetExecutingAssembly() });
        }
    }   
    ```
    > 最主要的是 注入这个程序集 ```services.AddMigratorAssembly(new Assembly[] { Assembly.GetExecutingAssembly() });```

- 配置迁移类（生成数据库表和初始化数据）
    1. 生成数据库表
    
        ```
        /// <summary>
        /// 建表
        /// </summary>
        [Migration(20220816103500)]
        public class AddLogTable : Migration
        {
            public override void Up()
            {
                Create.Table("Log")
                    .WithColumn("Id").AsInt64().PrimaryKey().Identity()
                    .WithColumn("Text").AsString();
            }

            public override void Down()
            {
                Delete.Table("Log");
            }
        }
        ```

    2. 初始化数据
    
        ```
        /// <summary>
        /// 初始化数据
        /// </summary>
        [Migration(20230816103500)]
        public class InitLogData : Migration
        {
            public override void Up()
            {
            Insert.IntoTable("Log")
                .Row(new { Id = 1, Text ="zeke" });
            }

            public override void Down()
            {
                Execute.Sql("delete * from log where id = 1");
            }
        }
        ```
    3. 注意事项
        > [Migration(数字)] ：```数字```必须唯一，将按照 ```数字``` 从小到大的顺序执行 （建议使用日期+时间方式）<br/>
        > 初始化数据迁移类 ```[Migration(数字)]``` 标签 中的数字一定要大于其使用的表的建数字，用人类的预言表述，就是必须有表之后才能插入数据<br/>

### 如何获取租户注入信息

```
    public class Test2Controller : ApiPermissionController
    {
        public Test2Controller()
        {
        }

        /// <summary>
        /// 获取访问Token
        /// </summary>
        /// <returns></returns>
        [ApiActionDescriptionAttribute("获取访问Token接口")]
        [NoPermission]
        [HttpGet]
        public ActionResult GetToken()
        {
            //租户信息
            var tenatninfo = TenantContext.CurrentTenant;
        } 
    }

```
> 通过 TenantContext 类，对租户信息进行了封装

