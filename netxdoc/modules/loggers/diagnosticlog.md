
## 系统诊断日志采用serilog记录到本地文件

```
{
  "Serilog": {
    "MinimumLevel": {
      "Default": "Information", //Verbose -> Debug -> Information -> Warning -> Error -> Fatal
      "Override": {
        "Microsoft": "Information",
        "Microsoft.AspNetCore": "Information"
      }
    },
    "WriteTo": [
      {
        "Name": "File",
        "Args": {
          "Path": "logs/netx-.log",
          "RollingInterval": "Day",
          "OutputTemplate": "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{RequestId}][{Level:u3}][{SourceContext}] {Message:lj}{NewLine}{Exception}",
          "Shared": true,
          "RollOnFileSizeLimit": true,
          "FileSizeLimitBytes": 1048576,
          "RetainedFileCountLimit": 1000
        }
      },
      {
        "Name": "Console",
        "Args": {}
      },
      {
        "Name": "Debug",
        "Args": {}
      }
    ]
  }
}
```

> 可自行修改日志存储介质