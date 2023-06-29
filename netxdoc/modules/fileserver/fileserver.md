## 配置说明

```
        {
        "fileserver": {
            "limitedsize": 20, // 单位 M
            "supportedext": ".jpg;.jpeg;.png"
        },
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

> limitedsize : 上传文件大小限制（M）

> supportedext : 支持的文件后缀

> rootpath : 文件保存本地的根目录

> requestpath : web请求前缀

> minetype : 自定义扩展 minetype

## 上传接口

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

## 如何实现自己的存储

计划开发中。。。 :rocket: