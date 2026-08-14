## JSON-RPC 规范

标准请求示例：

```json
{
    "jsonrpc":"2.0",
    "method":"user.info",
    "params":{"id":1},
    "id":1001
}
```

标准响应：

```json
{
    "jsonrpc":"2.0",
    "result":{"uid":1,"name":"test"},
    "id":1001
}
```

