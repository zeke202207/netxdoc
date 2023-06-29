# vben示例代码 :purple_heart:

## api重复调用  
  ```
  export function testRetry() {
    return defHttp.get(
      { url: Api.TestRetry },
      {
        retryRequest: {
          isOpenRetry: true,
          count: 5,
          waitTime: 1000,
        },
      },
    );
  }
    ```