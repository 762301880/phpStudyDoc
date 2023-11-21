



**登录接口的时候设置test**

![1642391519(1).jpg](https://s2.loli.net/2022/01/17/O4Eqz8dw2jPYXVA.png)

```shell
// 响应结果
const response = JSON.parse(responseBody)
//在控制台打印结果（在postman软件底部 Console 标签查看）
console.log(response);
 //储存到环境变量token
 pm.environment.set("api_token",response.data.jwtToken);
```

**使用**

> 使用的时候直接**引用变量即可**

![1642391837(1).jpg](https://s2.loli.net/2022/01/17/Z1qCU72THmbIozg.png)