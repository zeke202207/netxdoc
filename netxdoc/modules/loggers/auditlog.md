## 如何启用审计日志

 - 请启用 ```NetX.Audit``` 模块（关于如何启用module，[请参考](/netxdoc/startup.md))
 - 禁用审计 
    - 卸载```NetX.Audit```模块
    - ```NetX.Audit```模块，配置文件```enabled```设置为```false```

 ## 如何使用审计日志

 - 在需要审计的 ```controller - action```  上添加 ```[Audited]``` 标签
 - 在不需要审计的 ```controller - action```  上添加 ```[DisableAuditing]``` 标签