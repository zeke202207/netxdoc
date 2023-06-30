## 内置了简单文件服务器(SimpleFileSystem)，需要自己实现自己的存储即可


### 配置说明

```
        {
        "fileserver": {
            "limitedsize": 20, // 单位 M
            "supportedext": ".jpg;.jpeg;.png"
        },
```

> limitedsize : 上传文件大小限制（M）

> supportedext : 支持的文件后缀

### 上传接口

```
//单文件上传
[HttpPost("/netx/upload/{slug}")]
public async Task<ResultModel> Upload(IFormFile file, int slug)

//多文件上传
[HttpPost("/netx/uploadbatch/{slug}")]
public async Task<ResultModel> UploadBatch(IFormFileCollection file, int slug)
```
> slug 类型定义如下

```
        /// <summary>
        /// 文件类型
        /// </summary>
        public enum FileType : int
        {
            /// <summary>
            /// 图片
            /// </summary>
            Image = 0,

            /// <summary>
            /// 音频
            /// </summary>
            Audio,

            /// <summary>
            /// 视频
            /// </summary>
            Video,

            /// <summary>
            /// 其他
            /// </summary>
            Others,
        }
```


## XetX实现的本地存储(拿来可用)

### 配置说明

```
        "localstore": {
            "rootpath": "D:\\FileStore",
            "requestpath": "/zeke",
            "minetype": [
            {
                "key": ".m3u8",
                "value": "application/x-mpegURL"
            },
            {
                "key": ".3gp",
                "value": "video/3gpp"
            }
            ]
        }
        }
```


> rootpath : 文件保存本地的根目录

> requestpath : web请求前缀

> minetype : 自定义扩展 minetype

## 如何实现自己的存储

### 注入你的服务

```
public class YourInitializer : ModuleInitializer
{    
    /// <summary>
    /// 配置中间件
    /// </summary>
    /// <param name="app"></param>
    /// <param name="env"></param>
    /// <param name="context"></param>
    public override void ConfigureApplication(IApplicationBuilder app, IWebHostEnvironment env, ModuleContext context)
    {
        app.UseSimpleFileSystem(context, (app) =>
        {
           //Your code
        });
    }

    /// <summary>
    /// 服务注入
    /// </summary>
    /// <param name="services"></param>
    /// <param name="env"></param>
    /// <param name="context"></param>
    public override void ConfigureServices(IServiceCollection services, IWebHostEnvironment env, ModuleContext context)
    {
        services.AddSimpleFileSystem(context,
        s =>
        {
           //Your code
        });
    }
}
```
###  实现你的存储

```
public class YourStore : IFileStore
{
    //实现接口，并编写自己的 读、写 功能
}
```
so easy :rocket: